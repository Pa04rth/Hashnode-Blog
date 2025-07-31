---
title: "Building  a Chess.io"
datePublished: Tue Aug 13 2024 13:24:42 GMT+0000 (Coordinated Universal Time)
cuid: clzsgflvk000j0ajvghsm822q
slug: building-a-chessio
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/CNBRg1K9QvQ/upload/0368fa2e013e724cdbc79834b9136a3d.jpeg
tags: web-development, nodejs, game-development, expressjs-cilb5apda0066e053g7td7q24, chessjs

---

---

### LINK TO PROJECT-

[Chess.io](https://github.com/Pa04rth/CHESS_GAME)

---

### BASIC ROADMAP -

* Here we have used Express,Chess.js{for chess rules},EJS{frontend},Web-sockets ,WebRTC and Simple Peers library for the video call features.
    
* Create 3 files -&gt; 1-app.js {which is responsible for server side code}, 2- Chessgame.js {which will be responsible for the main chess game},3-index.ejs {which we will write for the frontend part}
    
* After linking them and coding them create video containers and use simple peers to maintain a peer connection between them
    
* And Yes !! you have create a fully functioning game.
    

---

### BACKEND STEPS {Till chess game}-

* **Import**: express, http, socket.io, chess.js
    
* **Create Express app instance** - Initialize HTTP server with Express, - Instantiate Socket.io on HTTP server
    
* **Initialize**: - Players object: track socket IDs, roles (white/black), - CurrentPlayer: track current turn
    
* **Configure Express app**: - Use EJS templating engine - Serve static files from 'public' directory.
    
* **Define route for root URL** - Render EJS template "index" - Title: "Custom Chess Game".
    
* **Socket.io handles connection event** - Callback executed on client connect - Server assigns role based on availability: -
    
* If slots empty: - Assign role (white/black) - Inform player -
    
* If slots full: - Designate as spectator.
    
* **Client connection**: - Assign role based on game state: - If no white player, assign white role - If no black player, assign black role - Emit "playerRole" event with assigned role - If both slots filled, designate as spectator - Emit "spectatorRole" event - Send initial board state using FEN notation
    
* **Client disconnection**: - Remove assigned role from players object
    
* **Listen for "move" events**: - Validate correct player's turn - If valid: - Update game state - Broadcast move via "move" event - Send updated board state via "boardState" event - If invalid: - Log error message
    
* **Ensure smooth gameplay and real-time updates for all connected clients.**
    

### FORNTEND {Till Chess game}-

* [**Socket.io**](http://Socket.io) **Initialization:** Establish WebSocket connection to the server using [Socket.io](http://Socket.io) (const socket = io();).
    
* **Chess Game Initialization:** Create an instance of the Chess class from the chess.js library to manage the game logic (const chess = new Chess();).
    
* **DOM Elements**: Select the HTML element with the ID "chessboard" to render the chessboard (const boardElement = document.querySelector("#chessboard");).
    
* **Drag and Drop:** Implement drag and drop functionality for moving chess pieces on the board. Pieces are draggable only if it's the player's turn. Event listeners for drag start, drag end, drag over, and drop events are attached to handle drag and drop interactions.
    
* **Rendering the Board:** Generate the HTML representation of the chessboard based on the current game state. Iterate over the board array and create square elements for each cell. Create piece elements for occupied squares and append them to square elements. Flip the board for the black player's view.
    
* **Handling Moves:** Handle player moves when dragging and dropping pieces. Construct a move object containing the source and target squares in algebraic notation. Emit a "move" event to the server via [Socket.io](http://Socket.io). Unicode Chess Pieces: Return Unicode characters representing chess pieces based on their type. [Socket.io](http://Socket.io)
    
* **Event Handlers:** Listen for various events from the server such as player role assignment, spectator role assignment, board state updates, and opponent moves. Update the local game state and render the board accordingly when receiving events.
    
* **Initial Rendering:** Call the renderBoard function initially to render the initial state of the chessboard.
    

### ADDING THE VIDEO STREAM FEATURE-

* To add the video streaming feature first download the simple peer library and initiate it.
    
* All its changes will be done in the Chessgame.js file only
    
* Add the video container in the index.ejs file
    
* Just by reading the simple peer docs you will be easily setup the peer connection.
    

And that's it you have finally made your own Chess game !!!

Enjoyy.....Happy coding ...