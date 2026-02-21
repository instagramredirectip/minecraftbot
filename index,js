const mineflayer = require('mineflayer');
const express = require('express');

// ==========================================
// 1. WEB SERVER (KEEPS RENDER AWAKE)
// ==========================================
const app = express();
const port = process.env.PORT || 3000;

// This provides a URL for UptimeRobot to ping
app.get('/', (req, res) => {
    res.send('Minecraft AFK Bot is running 24/7!');
});

app.listen(port, () => {
    console.log(`Web server listening on port ${port}`);
});

// ==========================================
// 2. MINECRAFT BOT LOGIC
// ==========================================
const serverConfig = {
    host: 'home-IVLn.aternos.me', // Replace with your Aternos IP
    // Mineflayer automatically resolves Aternos SRV ports, so you usually don't need a port here!
    username: 'Sura', // The name of your bot
    version: false // Automatically detects your server's version
};

function createBot() {
    console.log("Attempting to connect to the server...");
    const bot = mineflayer.createBot(serverConfig);

    bot.on('login', () => {
        console.log(`✅ Success! ${bot.username} has logged into the server.`);
    });

    // Anti-AFK System: Randomized activity to mimic a real player
    bot.on('spawn', () => {
        console.log("Bot has spawned in the world. Starting randomized activity...");
        scheduleRandomAction();
    });

    function scheduleRandomAction() {
        // Pick a random interval between 30 seconds and 3 minutes
        const nextDelay = Math.floor(Math.random() * (180000 - 30000 + 1)) + 30000;
        setTimeout(() => {
            performRandomAction();
            scheduleRandomAction(); // Schedule the next one
        }, nextDelay);
    }

    function performRandomAction() {
        // List of possible actions to fake human movement
        const actions = ['jump', 'forward', 'back', 'left', 'right', 'sneak', 'swing', 'look'];
        const action = actions[Math.floor(Math.random() * actions.length)];
        
        console.log(`[Anti-AFK] Performing random action: ${action}`);

        if (action === 'swing') {
            bot.swingArm('right');
        } else if (action === 'look') {
            const yaw = Math.random() * Math.PI * 2; // Random direction
            const pitch = (Math.random() * Math.PI) - (Math.PI / 2); // Random pitch
            bot.look(yaw, pitch, true);
        } else {
            bot.setControlState(action, true);
            // Hold the key for a random duration between 0.5s and 2s
            const holdTime = Math.floor(Math.random() * 1500) + 500;
            setTimeout(() => {
                bot.setControlState(action, false);
            }, holdTime);
        }
    }

    // Auto-Reconnect System: If the server kicks the bot, it will try to rejoin.
    bot.on('end', (reason) => {
        console.log(`❌ Bot disconnected. Reason: ${reason}`);
        console.log("Reconnecting in 15 seconds...");
        setTimeout(createBot, 15000);
    });

    bot.on('error', (err) => {
        console.log(`⚠️ Error encountered: ${err}`);
    });
}

// Start the bot
createBot();
