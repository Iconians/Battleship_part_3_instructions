# Battleship Part 3: Browser Edition with AI Integration

**Learning Objectives**
- Rebuild game using DOM manipulation
- Handle click events
- Create visual game interface
- Integrate OpenAI API for intelligent AI opponent
- Make API calls using fetch and async/await
- Handle API responses and errors
- Manage complex game state in browser

**Project Requirements**

Rebuild Battleship as a browser-based game with full UI and AI-powered opponent:

**Features:**
- Visual 10x10 grid rendered in browser
- Clickable tiles for guessing
- Two grids: Player's ships and Computer's grid
- **OpenAI-powered AI opponent** that makes intelligent strategic guesses
- Visual feedback: hits (red), misses (white/blue), sunk ships
- Ship placement interface (drag or click to place)
- Game state management (track turns, wins/losses)
- Restart game functionality
- Score tracking
- Animations for hits/misses (optional)
- Loading states for AI API calls
- Error handling for API failures

**AI Integration Requirements:**

The computer opponent must use OpenAI API to make intelligent guesses. The AI should:
1. Receive game state (grid, previous guesses, hits, misses, sunk ships)
2. Send a prompt to OpenAI describing the current game situation
3. Parse OpenAI's response to get the next guess coordinates
4. Handle API errors gracefully (fallback to simple AI if API fails)
5. Show loading state while waiting for AI response

**Implementation Guide:**

```javascript
// HTML structure
<div id="game-container">
    <div id="player-grid"></div>
    <div id="computer-grid"></div>
    <div id="game-info">
        <div id="ai-loading" style="display: none;">AI is thinking...</div>
    </div>
</div>

// JavaScript structure
class BattleshipGame {
    constructor() {
        this.playerGrid = createGrid(10, 10);
        this.computerGrid = createGrid(10, 10);
        this.playerShips = [];
        this.computerShips = [];
        this.gameState = 'placing'; // 'placing', 'playing', 'gameOver'
        this.computerGuesses = [];
        this.computerHits = [];
    }
    
    renderGrids() {
        // Create DOM elements for grids
        // Add click handlers
        // Update visual state
    }
    
    handlePlayerGuess(row, col) {
        // Check hit/miss
        // Update grid
        // Check for sunk ships
        // Computer's turn (calls AI)
    }
    
    async computerGuess() {
        // Show loading state
        document.querySelector('#ai-loading').style.display = 'block';
        
        try {
            // Get AI suggestion from OpenAI
            const guess = await this.getAIGuess();
            this.makeComputerGuess(guess.row, guess.col);
        } catch (error) {
            console.error('AI API failed:', error);
            // Fallback to simple AI
            const guess = this.simpleAIGuess();
            this.makeComputerGuess(guess.row, guess.col);
        } finally {
            document.querySelector('#ai-loading').style.display = 'none';
        }
    }
    
    async getAIGuess() {
        // Prepare game state for AI
        const gameState = {
            grid: this.computerGrid,
            guesses: this.computerGuesses,
            hits: this.computerHits,
            sunkShips: this.getSunkShips()
        };
        
        // Create prompt for OpenAI
        const prompt = this.createAIPrompt(gameState);
        
        // Call OpenAI API
        const response = await fetch('https://api.openai.com/v1/chat/completions', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${API_KEY}`
            },
            body: JSON.stringify({
                model: 'gpt-3.5-turbo',
                messages: [
                    {
                        role: 'system',
                        content: 'You are a Battleship game AI. Respond with only coordinates in format "A5" or "row,col" (0-9).'
                    },
                    {
                        role: 'user',
                        content: prompt
                    }
                ],
                max_tokens: 10,
                temperature: 0.7
            })
        });
        
        if (!response.ok) {
            throw new Error('OpenAI API request failed');
        }
        
        const data = await response.json();
        const aiResponse = data.choices[0].message.content.trim();
        
        // Parse AI response to get coordinates
        return this.parseAICoordinates(aiResponse);
    }
    
    createAIPrompt(gameState) {
        return `You are playing Battleship. Here's the current state:
        
Grid size: 10x10
Your previous guesses: ${gameState.guesses.map(g => `${g.row},${g.col}`).join('; ')}
Your hits: ${gameState.hits.map(h => `${h.row},${h.col}`).join('; ')}
Sunk ships: ${gameState.sunkShips.length}

Make your next strategic guess. Respond with coordinates only (e.g., "A5" or "3,7").`;
    }
    
    parseAICoordinates(response) {
        // Parse "A5" or "3,7" format to row, col
        // Handle various formats AI might return
        // Return { row: number, col: number }
    }
    
    simpleAIGuess() {
        // Fallback: simple random or hunt/target AI
        // Use this if OpenAI API fails
    }
}
```

**Key Concepts:**
- DOM manipulation (createElement, appendChild)
- Event handling (click events)
- CSS for styling grids
- State management (tracking game state)
- Modular JavaScript (separate game logic from DOM)
- **Async/await for API calls**
- **Fetch API for HTTP requests**
- **OpenAI API integration**
- **Error handling and fallback strategies**
- **Loading states for async operations**

**AI Implementation Options:**

**Option 1: Full OpenAI Integration (Recommended)**
- Use OpenAI API for all computer guesses
- Send game state to AI
- Parse AI responses
- Handle errors with fallback

**Option 2: Hybrid Approach**
- Use OpenAI for strategic decisions (after first hit)
- Use simple AI for initial random guesses
- Combine both approaches

**Option 3: OpenAI for Strategy Only**
- Use OpenAI to generate overall strategy
- Use local AI to execute the strategy
- Less API calls, lower cost

**Deliverables:**
- `index.html` - Game page
- `css/game.css` - Styling
- `js/game.js` - Main game logic
- `js/ai.js` - OpenAI API integration and fallback AI
- `js/grid.js` - Grid rendering
- `config.js` - API key configuration (add to .gitignore!)
- `.gitignore` - Ensure config.js is not committed
- Working game in browser
- Responsive design
- Error handling for API failures
- Loading states for AI responses

**Grading Criteria:**
- Game works in browser
- Visual grid is clear and interactive
- OpenAI API integration works correctly
- Fallback AI works when API fails
- Ship placement works
- Game state is managed correctly
- Code is modular and organized
- API key is stored securely (not in Git)
- Error handling is comprehensive
- Loading states provide good UX
- Good user experience

**Important Notes:**
- **Never commit API keys to Git** - Add `config.js` to `.gitignore`
- Handle API rate limits and errors gracefully
- Consider API costs - limit tokens and requests
- Test with fallback AI when API is unavailable
- Provide clear feedback when AI is "thinking"