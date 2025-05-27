<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guess the Result Game</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom styles to enhance Tailwind defaults and ensure responsiveness */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700;800;900&display=swap');

        body {
            font-family: 'Inter', sans-serif;
            background: linear-gradient(135deg, #0f172a 0%, #1e293b 50%, #0f172a 100%); /* Deeper, more dynamic background */
            color: #e2e8f0; /* Light text */
            display: flex;
            justify-content: center;
            align-items: flex-start; /* Align items to the start for better layout on smaller screens */
            min-height: 100vh;
            padding: 20px;
            box-sizing: border-box;
            background-attachment: fixed; /* Keep background fixed during scroll */
        }

        .container {
            max-width: 900px;
            width: 100%;
            background-color: rgba(30, 41, 59, 0.95); /* Slightly lighter dark background for container with transparency */
            border-radius: 1.5rem; /* Rounded corners */
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.7), 0 10px 10px -5px rgba(0, 0, 0, 0.5); /* Deeper shadow */
            padding: 2rem;
            display: flex;
            flex-direction: column;
            gap: 1.5rem; /* Slightly reduced gap */
            border: 1px solid rgba(71, 85, 105, 0.5); /* Subtle border */
            backdrop-filter: blur(5px); /* Frosted glass effect */
        }

        h1, h2 {
            color: #63b3ed; /* Blueish header color */
            text-align: center;
            font-weight: 800; /* Extra bold */
            margin-bottom: 1rem;
            text-shadow: 0 2px 4px rgba(0, 0, 0, 0.5); /* Text shadow for headers */
        }

        .section-card {
            background-color: rgba(30, 41, 59, 0.8); /* Even darker background for sections with transparency */
            border-radius: 1rem;
            padding: 1.5rem;
            box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.6), 0 10px 15px -3px rgba(0, 0, 0, 0.3); /* Inner and outer shadow */
            border: 1px solid rgba(71, 85, 105, 0.4); /* Subtle border */
        }

        .timer-display {
            font-size: 4rem; /* Even larger timer font */
            font-weight: 900;
            color: #f6e05e; /* Yellow timer color */
            text-shadow: 0 0 15px rgba(246, 224, 94, 0.8); /* Stronger glow effect */
            animation: pulse 1.5s infinite alternate; /* Pulsing animation */
        }

        @keyframes pulse {
            from { transform: scale(1); opacity: 1; }
            to { transform: scale(1.05); opacity: 0.9; }
        }

        .result-display {
            font-size: 1.75rem; /* Larger result font */
            font-weight: 700;
            color: #9ae6b4; /* Greenish result color */
            text-shadow: 0 1px 3px rgba(0, 0, 0, 0.4);
        }

        .guess-button {
            padding: 0.75rem 1.5rem;
            border-radius: 9999px; /* Pill shape */
            font-weight: 700;
            transition: all 0.3s ease-in-out; /* Smoother transitions */
            transform: scale(1);
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.4), 0 2px 4px -1px rgba(0, 0, 0, 0.3);
            border: none;
            cursor: pointer;
            outline: none; /* Remove outline on focus */
            position: relative; /* For pseudo-elements */
            overflow: hidden; /* For hover effects */
        }

        .guess-button::before {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            width: 300%;
            height: 300%;
            background: rgba(255, 255, 255, 0.1);
            transition: all 0.4s ease-in-out;
            border-radius: 50%;
            z-index: 0;
            transform: translate(-50%, -50%) scale(0);
        }

        .guess-button:hover:not(:disabled)::before {
            transform: translate(-50%, -50%) scale(1);
            opacity: 0;
        }

        .guess-button span {
            position: relative;
            z-index: 1;
        }

        .guess-button:hover:not(:disabled) {
            transform: scale(1.08); /* More pronounced hover */
            box-shadow: 0 15px 25px -5px rgba(0, 0, 0, 0.5), 0 5px 5px -5px rgba(0, 0, 0, 0.4);
        }

        .guess-button.selected {
            transform: scale(1.15); /* More pronounced selected */
            box-shadow: 0 15px 25px -5px rgba(0, 0, 0, 0.7), 0 5px 5px -5px rgba(0, 0, 0, 0.5);
            border: 4px solid #f6e05e; /* Thicker, glowing border for selected */
            filter: drop-shadow(0 0 8px rgba(246, 224, 94, 0.7)); /* Glow effect */
        }

        .guess-button:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: scale(1); /* No scale on disabled */
            box-shadow: none;
            filter: none;
        }

        /* Specific button colors with gradients */
        .btn-green { background: linear-gradient(45deg, #10b981, #059669); color: white; } /* Emerald green */
        .btn-green:hover:not(:disabled) { background: linear-gradient(45deg, #059669, #10b981); }
        .btn-white { background: linear-gradient(45deg, #e2e8f0, #cbd5e0); color: #2d3748; } /* Light gray */
        .btn-white:hover:not(:disabled) { background: linear-gradient(45deg, #cbd5e0, #e2e8f0); }
        .btn-orange { background: linear-gradient(45deg, #f97316, #ea580c); color: white; } /* Darker orange */
        .btn-orange:hover:not(:disabled) { background: linear-gradient(45deg, #ea580c, #f97316); }
        .btn-blue { background: linear-gradient(45deg, #3b82f6, #2563eb); color: white; } /* Stronger blue */
        .btn-blue:hover:not(:disabled) { background: linear-gradient(45deg, #2563eb, #3b82f6); }
        .btn-red { background: linear-gradient(45deg, #ef4444, #dc2626); color: white; } /* Stronger red */
        .btn-red:hover:not(:disabled) { background: linear-gradient(45deg, #dc2626, #ef4444); }
        .btn-purple { background: linear-gradient(45deg, #8b5cf6, #7c3aed); color: white; } /* New purple for recharge */
        .btn-purple:hover:not(:disabled) { background: linear-gradient(45deg, #7c3aed, #8b5cf6); }


        /* Number specific colors based on game logic */
        .num-0-5 { background: linear-gradient(to bottom right, #10b981, #f97316); color: white; } /* Mixed gradient */
        .num-green { background-color: #10b981; color: white; }
        .num-orange { background-color: #f97316; color: white; }

        /* History table styling */
        .history-table {
            width: 100%;
            border-collapse: separate; /* For rounded corners on cells */
            border-spacing: 0;
            overflow: hidden; /* Ensures rounded corners are applied */
            background-color: rgba(15, 23, 42, 0.7); /* Darker table background */
            border-radius: 0.75rem;
        }

        .history-table th, .history-table td {
            padding: 0.85rem 1.5rem; /* Slightly more padding */
            text-align: left;
            border-bottom: 1px solid rgba(71, 85, 105, 0.3); /* Lighter border */
        }

        .history-table th {
            background-color: rgba(71, 85, 105, 0.6); /* Darker header background */
            color: #cbd5e0;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 0.05em;
            font-size: 0.875rem;
        }

        .history-table tbody tr:last-child td {
            border-bottom: none;
        }

        .history-table tbody tr:nth-child(odd) {
            background-color: rgba(23, 33, 49, 0.7); /* Zebra stripes */
        }
        .history-table tbody tr:nth-child(even) {
            background-color: rgba(15, 23, 42, 0.7); /* Zebra stripes */
        }

        .history-table tbody tr:hover {
            background-color: rgba(45, 55, 72, 0.8); /* Darker hover for rows */
        }

        /* Color indicator in history */
        .color-indicator {
            width: 1.1rem; /* Slightly larger */
            height: 1.1rem;
            border-radius: 50%;
            display: inline-block;
            vertical-align: middle;
            margin-right: 0.6rem;
            border: 1px solid rgba(255,255,255,0.3);
            box-shadow: 0 0 5px rgba(255,255,255,0.2); /* Subtle glow */
        }

        .bg-color-green { background-color: #10b981; }
        .bg-color-orange { background-color: #f97316; }
        .bg-color-mixed { background: linear-gradient(to bottom right, #10b981, #f97316); }
        .bg-color-white { background-color: #e2e8f0; }

        /* General Modal Styling */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.8); /* Darker overlay */
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            backdrop-filter: blur(8px); /* Stronger blur */
            opacity: 0;
            transition: opacity 0.3s ease-out;
        }

        .modal-overlay.show {
            opacity: 1;
        }

        .modal-content {
            background-color: #2d3748;
            padding: 2.5rem; /* More padding */
            border-radius: 1.25rem; /* More rounded */
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.6); /* Deeper shadow */
            text-align: center;
            max-width: 450px; /* Slightly wider */
            width: 90%;
            border: 2px solid #63b3ed;
            animation: fadeInScale 0.3s cubic-bezier(0.68, -0.55, 0.27, 1.55) forwards; /* Bouncier animation */
        }

        @keyframes fadeInScale {
            0% { opacity: 0; transform: scale(0.7); }
            100% { opacity: 1; transform: scale(1); }
        }

        .modal-content h3 {
            color: #f6e05e;
            font-size: 2rem; /* Larger title */
            margin-bottom: 1rem;
            text-shadow: 0 1px 3px rgba(0, 0, 0, 0.4);
        }

        .modal-content p {
            font-size: 1.25rem; /* Larger message */
            margin-bottom: 1.75rem;
            color: #cbd5e0;
        }

        .modal-close-btn {
            background: linear-gradient(45deg, #63b3ed, #4299e1); /* Gradient button */
            color: white;
            padding: 0.85rem 2.5rem; /* More padding */
            border-radius: 9999px;
            font-weight: 700;
            transition: all 0.3s ease-in-out;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
        }

        .modal-close-btn:hover {
            background: linear-gradient(45deg, #4299e1, #63b3ed);
            transform: translateY(-2px) scale(1.02); /* Lift and slight scale */
            box-shadow: 0 6px 10px rgba(0, 0, 0, 0.4);
        }

        /* Betting Modal Specific Styles */
        #bettingModal .modal-content input[type="number"] {
            width: 80%;
            padding: 0.75rem;
            margin-bottom: 1.5rem;
            border-radius: 0.5rem;
            border: 1px solid #475569;
            background-color: #1e293b;
            color: #e2e8f0;
            font-size: 1.25rem;
            text-align: center;
            box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.4);
            transition: border-color 0.2s, box-shadow 0.2s;
        }

        #bettingModal .modal-content input[type="number"]:focus {
            outline: none;
            border-color: #63b3ed;
            box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.4), 0 0 0 3px rgba(99, 179, 237, 0.3);
        }

        #bettingModal .modal-content .button-group {
            display: flex;
            justify-content: center;
            gap: 1rem;
        }

        #bettingModal .modal-content .button-group button {
            flex: 1;
            max-width: 150px;
            padding: 0.85rem 1.5rem;
            border-radius: 9999px;
            font-weight: 700;
            transition: all 0.3s ease-in-out;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
        }
        #bettingModal .modal-content .button-group .btn-confirm {
            background: linear-gradient(45deg, #10b981, #059669);
            color: white;
        }
        #bettingModal .modal-content .button-group .btn-confirm:hover {
            background: linear-gradient(45deg, #059669, #10b981);
            transform: translateY(-2px) scale(1.02);
            box-shadow: 0 6px 10px rgba(0, 0, 0, 0.4);
        }
        #bettingModal .modal-content .button-group .btn-cancel {
            background: linear-gradient(45deg, #ef4444, #dc2626);
            color: white;
        }
        #bettingModal .modal-content .button-group .btn-cancel:hover {
            background: linear-gradient(45deg, #dc2626, #ef4444);
            transform: translateY(-2px) scale(1.02);
            box-shadow: 0 6px 10px rgba(0, 0, 0, 0.4);
        }


        /* Responsive adjustments */
        @media (max-width: 768px) {
            .container {
                padding: 1rem;
                gap: 1rem;
            }
            h1 { font-size: 2.5rem; }
            h2 { font-size: 1.75rem; }
            .timer-display { font-size: 3rem; }
            .result-display { font-size: 1.5rem; }
            .guess-button { padding: 0.6rem 1.2rem; font-size: 0.9rem; }
            .number-grid { grid-template-columns: repeat(5, 1fr); } /* Adjust grid for numbers on small screens */
            .history-table th, .history-table td { padding: 0.5rem 0.75rem; font-size: 0.75rem; }
            .modal-content { padding: 1.5rem; }
            .modal-content h3 { font-size: 1.5rem; }
            .modal-content p { font-size: 1rem; }
            .modal-close-btn { padding: 0.6rem 1.5rem; }
            #bettingModal .modal-content input[type="number"] { font-size: 1rem; }
            #bettingModal .modal-content .button-group button { padding: 0.75rem 1rem; font-size: 0.875rem; }
        }

        @media (max-width: 480px) {
            h1 { font-size: 2rem; }
            h2 { font-size: 1.5rem; }
            .timer-display { font-size: 2.5rem; }
            .result-display { font-size: 1.2rem; }
            .guess-button { padding: 0.5rem 1rem; font-size: 0.8rem; }
            .number-grid { grid-template-columns: repeat(4, 1fr); } /* Further adjust grid for numbers on very small screens */
            /* Removed bet-group styles as they are no longer used for betting inputs */
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="text-4xl md:text-5xl">Guess the Result Game</h1>

        <div class="section-card text-center">
            <h2 class="text-2xl md:text-3xl mb-4">Your User ID</h2>
            <p id="loadingMessage" class="text-lg text-blue-300">Initializing game...</p>
            <p class="text-lg mb-2 hidden" id="userIdDisplayContainer">User ID: <span id="displayUserId" class="font-mono text-yellow-300 break-all"></span></p>
        </div>

        <div class="section-card text-center hidden" id="walletSection">
            <h2 class="text-2xl md:text-3xl mb-4">Your Wallet</h2>
            <p class="text-4xl font-bold text-green-400 mb-4 flex items-center justify-center">
                <span class="text-5xl mr-2">💲</span><span id="userBalance">0.00</span>
            </p>
            <div class="flex flex-wrap justify-center gap-4 mt-4">
                <button class="guess-button btn-purple" data-recharge-amount="50" disabled><span>Recharge 💲50</span></button>
                <button class="guess-button btn-purple" data-recharge-amount="100" disabled><span>Recharge 💲100</span></button>
                <button class="guess-button btn-purple" data-recharge-amount="200" disabled><span>Recharge 💲200</span></button>
                <button class="guess-button btn-purple" data-recharge-amount="500" disabled><span>Recharge 💲500</span></button>
            </div>
        </div>

        <div class="section-card text-center hidden" id="gameSection">
            <div class="flex flex-col sm:flex-row justify-between items-center mb-4">
                <span class="text-xl font-semibold mb-2 sm:mb-0">Next Result In:</span>
                <span id="countdown" class="timer-display">30s</span>
            </div>
            <div class="text-lg md:text-xl mb-4">
                <span class="font-semibold">Last Result: </span>
                <span id="lastResult" class="result-display">Waiting for first result...</span>
            </div>
        </div>

        <div class="section-card hidden" id="guessingSection">
            <h2 class="text-2xl md:text-3xl">Make Your Guess</h2>

            <div class="mb-8">
                <h3 class="text-xl font-semibold mb-4">Choose Color:</h3>
                <div class="flex flex-wrap justify-center gap-6">
                    <button class="guess-button btn-green" data-guess-type="color" data-guess-value="Green" disabled><span>Green</span></button>
                    <button class="guess-button btn-white" data-guess-type="color" data-guess-value="White" disabled><span>White</span></button>
                    <button class="guess-button btn-orange" data-guess-type="color" data-guess-value="Orange" disabled><span>Orange</span></button>
                </div>
            </div>

            <div class="mb-8">
                <h3 class="text-xl font-semibold mb-4">Choose Number:</h3>
                <div class="grid grid-cols-5 md:grid-cols-10 gap-4 justify-items-center number-grid">
                    </div>
            </div>

            <div>
                <h3 class="text-xl font-semibold mb-4">Choose Big/Small:</h3>
                <div class="flex flex-wrap justify-center gap-6">
                    <button class="guess-button btn-blue" data-guess-type="bigSmall" data-guess-value="Big" disabled><span>Big</span></button>
                    <button class="guess-button btn-red" data-guess-type="bigSmall" data-guess-value="Small" disabled><span>Small</span></button>
                </div>
            </div>
        </div>

        <div class="section-card hidden" id="historySection">
            <h2 class="text-2xl md:text-3xl">Game History</h2>
            <div class="overflow-x-auto rounded-lg">
                <table class="history-table">
                    <thead>
                        <tr>
                            <th>Period</th>
                            <th>Number</th>
                            <th>Big/Small</th>
                            <th>Color</th>
                        </tr>
                    </thead>
                    <tbody id="gameHistoryBody">
                        </tbody>
                </table>
            </div>
            <p id="noHistoryMessage" class="text-center text-gray-400 py-4 hidden">No history yet. Play a round!</p>
        </div>
    </div>

    <div id="messageModal" class="modal-overlay hidden">
        <div class="modal-content">
            <h3 id="modalTitle"></h3>
            <p id="modalMessage"></p>
            <button id="modalCloseBtn" class="modal-close-btn">Got It!</button>
        </div>
    </div>

    <div id="bettingModal" class="modal-overlay hidden">
        <div class="modal-content">
            <h3 id="bettingModalTitle">Place Your Bet</h3>
            <p class="text-lg text-gray-300 mb-4">Current Balance: 💲<span id="bettingModalBalance">0.00</span></p>
            <input type="number" id="bettingAmountInput" placeholder="Enter bet amount" min="1" step="1" autofocus>
            <div class="button-group">
                <button id="bettingConfirmBtn" class="btn-confirm">Confirm Bet</button>
                <button id="bettingCancelBtn" class="btn-cancel">Cancel</button>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, setDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global Firebase variables
        let app;
        let auth;
        let db;
        let userId = null;
        let userBalanceRef = null; // Firestore document reference for user's balance

        // Global app ID for Firestore paths
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';

        // --- DOM Elements ---
        const countdownElement = document.getElementById('countdown');
        const lastResultElement = document.getElementById('lastResult');
        // Select all guess selection buttons (color, big/small, numbers will be dynamic)
        const allGuessSelectionButtons = document.querySelectorAll('.guess-button[data-guess-type]');
        const colorSelectionButtons = document.querySelectorAll('.guess-button[data-guess-type="color"]'); // Specifically for colors
        const bigSmallSelectionButtons = document.querySelectorAll('.guess-button[data-guess-type="bigSmall"]'); // Specifically for big/small
        const numberGrid = document.querySelector('.number-grid');
        const gameHistoryBody = document.getElementById('gameHistoryBody');
        const noHistoryMessage = document.getElementById('noHistoryMessage');

        // General Message Modal elements
        const messageModal = document.getElementById('messageModal');
        const modalTitle = document.getElementById('modalTitle');
        const modalMessage = document.getElementById('modalMessage');
        const modalCloseBtn = document.getElementById('modalCloseBtn');

        // Betting Input Modal elements
        const bettingModal = document.getElementById('bettingModal');
        const bettingModalTitle = document.getElementById('bettingModalTitle');
        const bettingAmountInput = document.getElementById('bettingAmountInput');
        const bettingConfirmBtn = document.getElementById('bettingConfirmBtn');
        const bettingCancelBtn = document.getElementById('bettingCancelBtn');
        const bettingModalBalance = document.getElementById('bettingModalBalance');

        const userBalanceElement = document.getElementById('userBalance');
        const displayUserId = document.getElementById('displayUserId');
        const loadingMessage = document.getElementById('loadingMessage');
        const userIdDisplayContainer = document.getElementById('userIdDisplayContainer');
        const rechargeButtons = document.querySelectorAll('[data-recharge-amount]'); // Select all recharge buttons

        // Game Section elements to show/hide based on auth state
        const walletSection = document.getElementById('walletSection');
        const gameSection = document.getElementById('gameSection');
        const guessingSection = document.getElementById('guessingSection');
        const historySection = document.getElementById('historySection');

        // Game State Variables
        let countdown = 30;
        let timerInterval;
        let gameHistory = [];
        let userBalance = 0; // Will be loaded from Firestore
        let isAuthReady = false; // Flag to indicate Firebase auth is ready

        // Multi-time betting: Store all bets for the current round
        let currentRoundBets = {
            color: [],      // Array of { amount: number, guess: string }
            number: [],     // Array of { amount: number, guess: number }
            bigSmall: []    // Array of { amount: number, guess: string }
        };

        // Variables to store the context for the betting modal
        let currentBetModalType = null;
        let currentBetModalValue = null;


        // --- Utility Functions ---

        /**
         * Updates the displayed user balance and saves to Firestore.
         */
        async function updateBalance(newBalance) {
            userBalance = newBalance;
            userBalanceElement.textContent = userBalance.toFixed(2); // No 💲 here, added in HTML
            bettingModalBalance.textContent = userBalance.toFixed(2); // Update balance in betting modal too
            if (userBalanceRef) {
                try {
                    await setDoc(userBalanceRef, { amount: userBalance });
                    console.log("Balance updated in Firestore:", userBalance);
                } catch (error) {
                    console.error("Error updating balance in Firestore:", error);
                    showMessageModal('Error', 'Failed to save balance. Please check your connection.');
                }
            }
        }

        /**
         * Displays a general custom modal message to the user.
         * @param {string} title - The title of the modal.
         * @param {string} message - The main message content.
         */
        function showMessageModal(title, message) {
            modalTitle.textContent = title;
            modalMessage.textContent = message;
            modalCloseBtn.textContent = 'Got It!';
            modalCloseBtn.onclick = hideMessageModal;
            messageModal.classList.remove('hidden');
            // Add 'show' class for fade-in effect
            setTimeout(() => messageModal.classList.add('show'), 10);
        }

        /**
         * Hides the general custom modal message.
         */
        function hideMessageModal() {
            modalTitle.textContent = '';
            modalMessage.textContent = '';
            messageModal.classList.remove('show'); // Remove 'show' class for fade-out
            setTimeout(() => {
                messageModal.classList.add('hidden');
            }, 300); // Wait for transition to finish
        }

        /**
         * Displays the betting modal.
         * @param {string} type - The type of bet (e.g., 'color', 'number', 'bigSmall').
         * @param {string|number} value - The specific value being bet on (e.g., 'Green', 5, 'Big').
         */
        function showBettingModal(type, value) {
            if (!isAuthReady) {
                showMessageModal('Game Not Ready', 'Please wait for the game to initialize to place bets.');
                return;
            }

            currentBetModalType = type;
            currentBetModalValue = value;

            bettingModalTitle.textContent = `Bet on ${value}`;
            bettingAmountInput.value = ''; // Clear previous input
            bettingModalBalance.textContent = userBalance.toFixed(2); // Update current balance
            bettingModal.classList.remove('hidden');
            setTimeout(() => {
                bettingModal.classList.add('show');
                bettingAmountInput.focus(); // Focus on the input field
            }, 10);
        }

        /**
         * Hides the betting modal.
         */
        function hideBettingModal() {
            bettingModal.classList.remove('show');
            setTimeout(() => {
                bettingModal.classList.add('hidden');
                currentBetModalType = null;
                currentBetModalValue = null;
            }, 300);
        }

        /**
         * Gets the appropriate CSS class for a number button based on game logic.
         * @param {number} num - The number to check.
         * @returns {string} - The CSS class string.
         */
        function getNumberColorClass(num) {
            if (num === 0 || num === 5) return 'num-0-5'; // Mixed color for 0 and 5
            if ([1, 3, 7, 9].includes(num)) return 'num-green'; // Green for odd numbers (excluding 5)
            return 'num-orange'; // Orange for even numbers (excluding 0)
        }

        /**
         * Generates the number buttons dynamically and attaches their event listeners.
         */
        function generateNumberButtons() {
            numberGrid.innerHTML = ''; // Clear existing buttons
            for (let i = 0; i < 10; i++) {
                const button = document.createElement('button');
                button.textContent = i;
                button.classList.add('guess-button', 'w-16', 'h-16', 'flex', 'items-center', 'justify-center', 'text-xl', getNumberColorClass(i)); /* Larger numbers */
                button.dataset.guessType = 'number';
                button.dataset.guessValue = i;
                button.disabled = !isAuthReady; // Disable until authenticated

                // Attach event listener directly to this new button
                button.addEventListener('click', (event) => {
                    if (!isAuthReady) {
                        showMessageModal('Game Not Ready', 'Please wait for the game to initialize.');
                        return;
                    }
                    const clickedButton = event.currentTarget;
                    const guessValue = clickedButton.dataset.guessValue;

                    // Remove 'selected' class from all number buttons
                    document.querySelectorAll('[data-guess-type="number"]').forEach(btn => {
                        btn.classList.remove('selected');
                    });

                    // Add 'selected' class to the clicked button
                    clickedButton.classList.add('selected');

                    // Show the betting modal for this number
                    showBettingModal('number', parseInt(guessValue));
                });

                numberGrid.appendChild(button);
            }
        }

        /**
         * Enables/disables game controls based on authentication status.
         * @param {boolean} enable - True to enable, false to disable.
         */
        function setGameControlsEnabled(enable) {
            allGuessSelectionButtons.forEach(button => { // Enable/disable all selection buttons
                button.disabled = !enable;
            });
            rechargeButtons.forEach(button => { // Enable/disable all recharge buttons
                button.disabled = !enable;
            });
            // Re-generate number buttons to apply disabled state correctly and attach listeners
            generateNumberButtons();
        }

        /**
         * Shows/hides game sections based on authentication status.
         * @param {boolean} show - True to show, false to hide.
         */
        function toggleGameSections(show) {
            walletSection.classList.toggle('hidden', !show);
            gameSection.classList.toggle('hidden', !show);
            guessingSection.classList.toggle('hidden', !show);
            historySection.classList.toggle('hidden', !show);
        }

        // --- Game Logic Functions ---

        /**
         * Starts or restarts the countdown timer.
         */
        function startTimer() {
            clearInterval(timerInterval); // Clear any existing timer to prevent multiple intervals
            countdown = 30; // Reset countdown for the new round
            countdownElement.textContent = `${countdown}s`; // Update display immediately
            console.log('Timer started, countdown:', countdown); // Log start

            // Reset all bets for the new round
            currentRoundBets = {
                color: [],
                number: [],
                bigSmall: []
            };

            // Reset selected values (visual only, actual bets are in currentRoundBets)
            // No need to explicitly reset selectedColor, selectedNumber, selectedBigSmall
            // as they are now only used as temporary holders for the modal trigger.

            // Reset UI for next round
            allGuessSelectionButtons.forEach(button => { // All selection buttons
                button.classList.remove('selected'); // Remove selected state
                button.disabled = !isAuthReady; // Re-enable guess selection buttons
            });
            generateNumberButtons(); // Re-render number buttons to re-enable them and remove selected state

            timerInterval = setInterval(() => {
                countdown--;
                countdownElement.textContent = `${countdown}s`;
                console.log('Countdown:', countdown); // Log countdown progress

                if (countdown <= 0) {
                    clearInterval(timerInterval); // Stop the timer
                    generateResult(); // Generate new game result
                    startTimer(); // Start timer for the next round
                }
            }, 1000); // Update every 1 second
        }

        /**
         * Generates a random game result (number, big/small, color).
         */
        function generateResult() {
            const randomNumber = Math.floor(Math.random() * 10); // Random number from 0 to 9
            let bigSmall = '';
            if (randomNumber >= 5) {
                bigSmall = 'Big';
            } else {
                bigSmall = 'Small';
            }

            // Apply special rules for 0 and 5 as seen in similar games
            if (randomNumber === 0) bigSmall = 'Small'; // 0 is typically small
            if (randomNumber === 5) bigSmall = 'Big';   // 5 is typically big

            let color = '';
            let colorClass = '';
            if (randomNumber === 0 || randomNumber === 5) {
                color = 'Green/Orange'; // Mixed color
                colorClass = 'bg-color-mixed';
            } else if ([1, 3, 7, 9].includes(randomNumber)) {
                color = 'Green';
                colorClass = 'bg-color-green';
            } else { // 2, 4, 6, 8
                color = 'Orange';
                colorClass = 'bg-color-orange';
            }

            const newResult = {
                period: new Date().toLocaleTimeString('en-US', { hour: '2-digit', minute: '2-digit', second: '2-digit', hour12: false }),
                number: randomNumber,
                bigSmall: bigSmall,
                color: color,
                colorClass: colorClass
            };

            // Update the last result display
            lastResultElement.textContent = `${newResult.number} (${newResult.bigSmall}, ${newResult.color})`;

            // Add the new result to game history
            addHistoryEntry(newResult);

            // Check the user's guess against the new result and calculate winnings
            if (isAuthReady) { // Only calculate winnings if authenticated
                calculateWinnings(newResult);
            } else {
                showMessageModal('Round Ended!', 'Game not fully initialized. Please refresh.');
            }
        }

        /**
         * Adds a new game result entry to the history table.
         * @param {object} result - The result object to add.
         */
        function addHistoryEntry(result) {
            gameHistory.unshift(result); // Add to the beginning of the array
            if (gameHistory.length > 10) {
                gameHistory.pop(); // Keep only the last 10 entries
            }
            renderHistory(); // Re-render the history table
        }

        /**
         * Renders the game history table from the gameHistory array.
         */
        function renderHistory() {
            gameHistoryBody.innerHTML = ''; // Clear existing history
            if (gameHistory.length === 0) {
                noHistoryMessage.classList.remove('hidden');
            } else {
                noHistoryMessage.classList.add('hidden');
                gameHistory.forEach(entry => {
                    const row = gameHistoryBody.insertRow();
                    row.innerHTML = `
                        <td class="py-3 px-6 whitespace-nowrap">${entry.period}</td>
                        <td class="py-3 px-6">${entry.number}</td>
                        <td class="py-3 px-6">${entry.bigSmall}</td>
                        <td class="py-3 px-6">
                            <span class="color-indicator ${entry.colorClass}"></span>
                            ${entry.color}
                        </td>
                    `;
                });
            }
        }

        /**
         * Calculates winnings based on the user's individual bets and the actual result.
         * Updates the user's balance and provides feedback.
         * @param {object} result - The actual game result.
         */
        async function calculateWinnings(result) {
            let totalWinningsFromRound = 0; // Total winnings for this round
            let winningBetsSummary = []; // To store messages for individual winning bets
            let totalBetsPlaced = 0; // To check if any bet was placed at all

            // Check Number Bets
            currentRoundBets.number.forEach(bet => {
                totalBetsPlaced++;
                const isNumberCorrect = (result.number === bet.guess);
                if (isNumberCorrect) {
                    const winnings = bet.amount * 10;
                    totalWinningsFromRound += winnings;
                    winningBetsSummary.push(`Number (${bet.guess}) bet of 💲${bet.amount.toFixed(2)}: Won 💲${winnings.toFixed(2)}`);
                }
            });

            // Check Color Bets
            currentRoundBets.color.forEach(bet => {
                totalBetsPlaced++;
                let isColorCorrect = false;
                if (result.color === 'Green/Orange') {
                    isColorCorrect = (bet.guess === 'Green' || bet.guess === 'Orange');
                } else {
                    isColorCorrect = (result.color === bet.guess);
                }

                if (isColorCorrect) {
                    const winnings = bet.amount * 2;
                    totalWinningsFromRound += winnings;
                    winningBetsSummary.push(`Color (${bet.guess}) bet of 💲${bet.amount.toFixed(2)}: Won 💲${winnings.toFixed(2)}`);
                }
            });

            // Check Big/Small Bets
            currentRoundBets.bigSmall.forEach(bet => {
                totalBetsPlaced++;
                const isBigSmallCorrect = (result.bigSmall === bet.guess);
                if (isBigSmallCorrect) {
                    const winnings = bet.amount * 2;
                    totalWinningsFromRound += winnings;
                    winningBetsSummary.push(`Big/Small (${bet.guess}) bet of 💲${bet.amount.toFixed(2)}: Won 💲${winnings.toFixed(2)}`);
                }
            });

            let finalMessage = '';
            const roundOutcome = `Result: Number ${result.number} (${result.bigSmall}, ${result.color})`;

            if (winningBetsSummary.length > 0) {
                finalMessage = `🏆 Congratulations! 🏆\n\n${roundOutcome}\n\n` +
                               `You won:\n` +
                               winningBetsSummary.join('\n') +
                               `\n\nTotal Winnings this round: 💲${totalWinningsFromRound.toFixed(2)}`;
            } else if (totalBetsPlaced > 0) {
                finalMessage = `${roundOutcome}\n\n` +
                               `You lost this round. Better luck next time!`;
            } else {
                finalMessage = `${roundOutcome}\n\n` +
                               `No bets were placed for this round.`;
            }

            // Update balance in Firestore
            const newBalance = userBalance + totalWinningsFromRound; // Total winnings are already net of losses from individual bets
            await updateBalance(newBalance);
            showMessageModal('Round Result!', `${finalMessage}\n\nYour new balance: 💲${userBalance.toFixed(2)}`);
        }

        // --- Firebase Initialization and Auth State Management ---

        /**
         * Initializes Firebase and sets up authentication.
         */
        async function initializeFirebase() {
            try {
                const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
                app = initializeApp(firebaseConfig);
                auth = getAuth(app);
                db = getFirestore(app);

                loadingMessage.textContent = 'Signing in...';

                // Use onAuthStateChanged to ensure the user object is fully ready and handle persistence
                auth.onAuthStateChanged(async (currentUser) => {
                    if (currentUser) {
                        userId = currentUser.uid;
                        displayUserId.textContent = userId;
                        userIdDisplayContainer.classList.remove('hidden'); // Show user ID
                        loadingMessage.classList.add('hidden'); // Hide loading message

                        // Set up Firestore reference for user's balance
                        userBalanceRef = doc(db, `artifacts/${appId}/users/${userId}/wallet/balance`);

                        // Listen for real-time updates to the balance
                        onSnapshot(userBalanceRef, (docSnap) => {
                            if (docSnap.exists()) {
                                userBalance = docSnap.data().amount || 0;
                                updateBalance(userBalance); // Use updateBalance to also update UI
                                console.log("Balance updated from Firestore snapshot:", userBalance);
                            } else {
                                // If balance document doesn't exist, initialize it
                                updateBalance(20); // New user starts with 💲20
                                console.log("Initialized new user balance to 💲20");
                            }
                            if (!isAuthReady) { // Only run once on initial load
                                isAuthReady = true;
                                toggleGameSections(true); // Show game sections
                                setGameControlsEnabled(true); // Enable game controls
                                startTimer(); // Start the game timer
                                showMessageModal('Welcome!', `Your user ID is: ${userId}. You have 💲${userBalance.toFixed(2)}.`);
                            }
                        }, (error) => {
                            console.error("Error listening to balance:", error);
                            showMessageModal('Error', 'Failed to load balance. Please check your connection.');
                            loadingMessage.textContent = 'Error loading user data.';
                        });

                    } else {
                        // No user is signed in, attempt anonymous sign-in
                        userId = null;
                        displayUserId.textContent = '';
                        userIdDisplayContainer.classList.add('hidden');
                        isAuthReady = false;
                        setGameControlsEnabled(false);
                        toggleGameSections(false);
                        loadingMessage.classList.remove('hidden');
                        loadingMessage.textContent = 'Signing in anonymously...';

                        try {
                            if (typeof __initial_auth_token !== 'undefined') {
                                await signInWithCustomToken(auth, __initial_auth_token);
                            } else {
                                await signInAnonymously(auth);
                            }
                        } catch (error) {
                            console.error("Error during anonymous sign-in:", error);
                            showMessageModal('Initialization Error', 'Failed to sign in. Please try refreshing the page.');
                            loadingMessage.textContent = 'Initialization failed.';
                        }
                    }
                });

            } catch (error) {
                console.error("Error initializing Firebase:", error);
                showMessageModal('Initialization Error', 'Failed to initialize the game. Please check your console for details.');
                loadingMessage.textContent = 'Game initialization failed.';
            }
        }

        // --- Event Listeners ---

        // Event listener for COLOR selection buttons (now trigger betting modal)
        colorSelectionButtons.forEach(button => {
            button.addEventListener('click', (event) => {
                if (!isAuthReady) {
                    showMessageModal('Game Not Ready', 'Please wait for the game to initialize.');
                    return;
                }
                const clickedButton = event.currentTarget;
                const guessValue = clickedButton.dataset.guessValue;

                // Remove 'selected' class from all color buttons
                colorSelectionButtons.forEach(btn => {
                    btn.classList.remove('selected');
                });
                clickedButton.classList.add('selected'); // Mark the clicked color as selected

                // Show the betting modal for this color
                showBettingModal('color', guessValue);
            });
        });

        // Event listener for BIG/SMALL selection buttons (now trigger betting modal)
        bigSmallSelectionButtons.forEach(button => {
            button.addEventListener('click', (event) => {
                if (!isAuthReady) {
                    showMessageModal('Game Not Ready', 'Please wait for the game to initialize.');
                    return;
                }
                const clickedButton = event.currentTarget;
                const guessValue = clickedButton.dataset.guessValue;

                // Remove 'selected' class from all big/small buttons
                bigSmallSelectionButtons.forEach(btn => {
                    btn.classList.remove('selected');
                });
                clickedButton.classList.add('selected'); // Mark the clicked big/small as selected

                // Show the betting modal for this big/small
                showBettingModal('bigSmall', guessValue);
            });
        });


        // Event listener for general message modal close button
        modalCloseBtn.addEventListener('click', hideMessageModal);

        // Event listener for betting modal confirm button
        bettingConfirmBtn.addEventListener('click', async () => {
            const betValue = parseFloat(bettingAmountInput.value);

            if (isNaN(betValue) || betValue <= 0) {
                showMessageModal('Invalid Bet', 'Please enter a valid bet amount greater than 0.');
                return;
            }

            if (betValue > userBalance) {
                showMessageModal('Insufficient Funds', `You only have 💲${userBalance.toFixed(2)}. Your bet of 💲${betValue.toFixed(2)} is too high.`);
                return;
            }

            // Deduct bet immediately from balance and update Firestore
            await updateBalance(userBalance - betValue);

            // Add the bet to the current round's appropriate bets array
            if (currentBetModalType === 'color') {
                currentRoundBets.color.push({ amount: betValue, guess: currentBetModalValue });
            } else if (currentBetModalType === 'number') {
                currentRoundBets.number.push({ amount: betValue, guess: currentBetModalValue });
            } else if (currentBetModalType === 'bigSmall') {
                currentRoundBets.bigSmall.push({ amount: betValue, guess: currentBetModalValue });
            }

            hideBettingModal(); // Hide the betting modal
            showMessageModal('Bet Placed!', `You have successfully placed a bet of 💲${betValue.toFixed(2)} on ${currentBetModalValue} (${currentBetModalType}).`);

            // Clear the selection on the button that triggered the modal
            // Note: For multi-betting, we don't necessarily clear the selection visually
            // unless the user explicitly cancels or a new round starts.
            // For now, we'll keep the selected state for visual feedback that they clicked it.
            // document.querySelectorAll(`[data-guess-type="${currentBetModalType}"]`).forEach(btn => btn.classList.remove('selected'));
        });

        // Event listener for betting modal cancel button
        bettingCancelBtn.addEventListener('click', () => {
            hideBettingModal();
            // Clear the selection on the button that triggered the modal if cancelled
            document.querySelectorAll(`[data-guess-type="${currentBetModalType}"]`).forEach(btn => btn.classList.remove('selected'));
            showMessageModal('Bet Cancelled', 'Your bet has been cancelled.');
        });


        // Event listener for ALL recharge buttons
        rechargeButtons.forEach(button => {
            button.addEventListener('click', async (event) => {
                if (!isAuthReady) {
                    showMessageModal('Game Not Ready', 'Please wait for the game to initialize to recharge.');
                    return;
                }
                const rechargeAmount = parseFloat(event.currentTarget.dataset.rechargeAmount);
                const newBalance = userBalance + rechargeAmount;
                await updateBalance(newBalance);
                showMessageModal('Recharge Successful!', `You have recharged 💲${rechargeAmount.toFixed(2)}. Your new balance is 💲${userBalance.toFixed(2)}.`);
            });
        });

        // --- Initialization ---

        // Generate number buttons initially (will be disabled until auth)
        generateNumberButtons();

        // Initialize Firebase when the window loads
        window.onload = initializeFirebase;
    </script>
</body>
</html>

