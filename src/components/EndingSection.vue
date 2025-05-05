<template>
  <section class="ending-section">
    <div class="content">
      <h2 class="title">Final Thoughts</h2>
      
      <!-- Chat bubbles area with all conversations -->
      <div class="chat-container">
        <ol class="chat-list">
          <!-- Conversation Pair 1 -->
          <li id="sender1" class="message shared sent">
            Hey AI, what's the weather today?
          </li>
          <li id="receiver1" class="message shared received">
            <div class="avatar-container">
              <div class="avatar"></div>
            </div>
            <div class="message-content">
              Look out the window or open a basic weather site, cause it takes way more energy for me to look that up for you.
            </div>
          </li>
          
          <!-- Conversation Pair 2 -->
          <li id="sender2" class="message shared sent">
            Translate 'bonjour' to English.
          </li>
          <li id="receiver2" class="message shared received">
            <div class="avatar-container">
              <div class="avatar"></div>
            </div>
            <div class="message-content">
              That one-word lookup would cost me more energy than it took the Romans to invent Latin. Spoiler: it's 'hello.' Voilà—no carbon guilt!
            </div>
          </li>
          
          <!-- Conversation Pair 3 -->
          <li id="sender3" class="message shared sent">
            What's 17 × 23?
          </li>
          <li id="receiver3" class="message shared received">
            <div class="avatar-container">
              <div class="avatar"></div>
            </div>
            <div class="message-content">
              A pocket calculator can answer before I even wake a server rack—and it won't need a mid-afternoon power snack.
            </div>
          </li>
          
          <!-- Conversation Pair 4 -->
          <li id="sender4" class="message shared sent">
            Who won the 1998 World Cup?
          </li>
          <li id="receiver4" class="message shared received">
            <div class="avatar-container">
              <div class="avatar"></div>
            </div>
            <div class="message-content">
              A two-second web search beats my whole-brain boot-up. Hint: 🐓 + 🇫🇷. Enjoy the trivia and the lower kilowatt-hours.
            </div>
          </li>
          
          <!-- Conversation Pair 5 -->
          <li id="sender5" class="message shared sent">
            What time is it in Tokyo right now?
          </li>
          <li id="receiver5" class="message shared received">
            <div class="avatar-container">
              <div class="avatar"></div>
            </div>
            <div class="message-content">
              Your phone's world-clock uses milliwatts. My cluster? Megawatts. Save the electrons—tap the clock widget.
            </div>
          </li>
          
          <!-- Conversation Pair 6 -->
          <li id="sender6" class="message shared sent">
            Define 'serendipity.'
          </li>
          <li id="receiver6" class="message shared received">
            <div class="avatar-container">
              <div class="avatar"></div>
            </div>
            <div class="message-content">
              Dictionary dot com can do that on less power than my GPUs use just to warm up. (It means a happy accident—like saving energy!)
            </div>
          </li>
        </ol>
      </div>
      
      <div class="text-columns">
        <div class="column">
          <p>
            We hope this project has shed light on the hidden environmental cost of using AI. Though ML systems unlock remarkable new capabilities, they also draw real power from real data centers and their servers leave a carbon trace. The charts you explored focus only on the energy used per inference, but it is worth considering that training today's large models is even more demanding; building GPT-3 is estimated to have consumed around 1,300Wh of electricity and released more than 500 CO₂ outputs in the early energy use of 100 U.S. homes. Some analyses place the total for state-of-the-art models an order of magnitude higher, at 10MWh, equivalent to powering over 1,000 households for a year.
          </p>
        </div>
        <div class="column">
          <p>
            Although the training cost is amortized over millions of later queries, each conversation you or your application has with AI still adds another watt-second to the tally. That means efficiency matters at every stage: smarter model architectures, cleaner data-centre energy mixes, thoughtful prompt design, and mindful user habits all add up.
          </p>
          <p>
            If we pair our enthusiasm for AI with equal determination to reduce its carbon cost, we can keep innovating without borrowing too much from the planet's future. That is the next challenge—and our shared opportunity.
          </p>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { onMounted, onBeforeUnmount } from 'vue';

onMounted(() => {
  // Get all conversation pairs
  const conversations = [
    { 
      sender: document.getElementById('sender1'), 
      receiver: document.getElementById('receiver1') 
    },
    { 
      sender: document.getElementById('sender2'), 
      receiver: document.getElementById('receiver2') 
    },
    { 
      sender: document.getElementById('sender3'), 
      receiver: document.getElementById('receiver3') 
    },
    { 
      sender: document.getElementById('sender4'), 
      receiver: document.getElementById('receiver4') 
    },
    { 
      sender: document.getElementById('sender5'), 
      receiver: document.getElementById('receiver5') 
    },
    { 
      sender: document.getElementById('sender6'), 
      receiver: document.getElementById('receiver6') 
    },
  ];
  
  // Initially hide all messages
  conversations.forEach(convo => {
    convo.sender.style.opacity = '0';
    convo.sender.style.transform = 'translateY(20px)';
    convo.sender.style.display = 'none';
    
    convo.receiver.style.opacity = '0';
    convo.receiver.style.transform = 'translateY(20px)';
    convo.receiver.style.display = 'none';
  });
  
  let isAnimating = true;
  let currentIndex = 0;
  
  // Function to animate a conversation pair
  const animateConversation = (index) => {
    const { sender, receiver } = conversations[index];
    
    return new Promise(resolve => {
      // Hide all other messages first
      conversations.forEach(convo => {
        convo.sender.style.opacity = '0';
        convo.sender.style.display = 'none';
        convo.receiver.style.opacity = '0';
        convo.receiver.style.display = 'none';
        convo.sender.classList.remove('pop');
        convo.receiver.classList.remove('pop');
      });
      
      // Show current sender and receiver
      sender.style.display = 'block';
      receiver.style.display = 'block';
      
      // Short delay before animation
      setTimeout(() => {
        // Animate sender first
        sender.style.opacity = '1';
        sender.style.transform = 'translateY(0)';
        
        // Add pop effect to sender
        setTimeout(() => {
          sender.classList.add('pop');
          
          // Then animate receiver after a short delay
          setTimeout(() => {
            receiver.style.opacity = '1';
            receiver.style.transform = 'translateY(0)';
            
            // Add pop effect to receiver
            setTimeout(() => {
              receiver.classList.add('pop');
              resolve();
            }, 300);
          }, 500); // Slightly longer delay to read the sender message
        }, 300);
      }, 300);
    });
  };
  
  // Function to run animation cycle
  const runAnimation = async () => {
    while (isAnimating) {
      // Animate current conversation
      await animateConversation(currentIndex);
      
      // Display time - increased to 11 seconds (5s reading time + 6s viewing time)
      await new Promise(resolve => setTimeout(resolve, 11000));
      
      // Move to next conversation
      currentIndex = (currentIndex + 1) % conversations.length;
    }
  };
  
  // Start animation
  runAnimation();
  
  // Cleanup
  onBeforeUnmount(() => {
    isAnimating = false;
  });
});
</script>

<style scoped>
.ending-section {
  position: relative;
  width: 100%;
  background: hsla(260,40%,5%,1);
  display: flex;
  align-items: flex-start;
  padding: 100px 0 0 0;
  box-sizing: border-box;
  margin-top: 60px;
}

.content {
  padding-left: 120px; /* Match the Introduction section's padding */
  width: 80%;
}

.title {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 50px;
  font-weight: 300;
  color: #fff;
  margin: 0 0 60px 0;
  opacity: 1;
}

/* Chat bubbles styling to match ChatGPT */
.chat-container {
  width: 100%;
  max-width: 100%; /* Allow full width */
  margin: 20px 0px 100px 100px;
  min-height: 400px;
  display: flex;
  justify-content: center;
  padding: 60px 60px; /* Add horizontal padding */
}

.chat-list {
  --sentColor: #303030; /* Dark gray for user messages */
  --receiveColor: #171717; /* Very dark gray for AI messages */
  --bg: hsla(260,40%,5%,1);
  
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0 auto; /* Center the list */
  padding: 0;
  list-style: none;
  width: 80%;
  max-width: 800px; /* Control maximum width */
  position: relative;
}

.message {
  position: relative;
  max-width: 80%;
  margin-bottom: 24px;
  padding: 16px 20px;
  line-height: 1.5;
  word-wrap: break-word;
  border-radius: 12px;
  font-family: 'Helvetica Neue', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  font-size: 20px;
  font-weight: 400;
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.6s ease, transform 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  will-change: opacity, transform;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

.sent {
  align-self: flex-end !important;
  color: #ffffff;
  background: var(--sentColor);
  margin-left: auto;
  margin-right: 0;
  max-width: 400px;
  border-radius: 12px;
}

.received {
  align-self: flex-start !important;
  color: #ffffff;
  background: var(--receiveColor);
  line-height: 1.5; 
  margin-left: 40px; /* Increased to make room for avatar */
  max-width: 500px;
  border-radius: 12px;
  display: flex;
  position: relative;
}

/* Avatar styling */
.avatar-container {
  position: absolute;
  left: -40px;
  bottom: 0;
  display: flex;
  align-items: flex-end;
  height: 100%;
}

.avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background-color: #10a37f; /* Restore the background color as fallback */
  background-image: url('/x.png'); /* Use x.png from root directory */
  background-size: cover;
  background-position: center;
  display: flex;
  justify-content: center;
  align-items: center;
  color: white;
  font-weight: bold;
  font-size: 16px;
}

/* Move the content in received messages to a separate div */
.message-content {
  flex: 1;
}

.message.pop {
  animation: subtle-pop 0.3s forwards;
}

@keyframes subtle-pop {
  0% { transform: scale(1); }
  50% { transform: scale(1.02); }
  100% { transform: scale(1); }
}

/* Remove tails by setting their display to none */
.shared:before, 
.shared:after {
  display: none; /* Hide the tails completely */
}

.text-columns {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
  margin-bottom: 40px;
  justify-content: space-between;
  width: 690px;
}

.column {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 16px;
  line-height: 1.6;
  color: #ffffffe2;
  opacity: 1;
  width: 98%;
}

.column p {
  margin-bottom: 24px;
}

.column p:last-child {
  margin-bottom: 0;
}

/* Responsive adjustments */
@media screen and (max-width: 1200px) {
  .title {
    font-size: 40px;
    margin: 0 0 30px 0;
  }
  
  .chat-container {
    padding: 0 40px;
  }
  
  .chat-list {
    width: 90%;
  }
}

@media screen and (max-width: 768px) {
  .ending-section {
    padding: 60px 0 0 0;
  }

  .content {
    padding-left: 20px; /* Match the Introduction section's mobile padding */
  }
  
  .text-columns {
    grid-template-columns: 1fr;
    gap: 10px;
    justify-content: flex-start;
    width: 100%;
    max-width: 620px;
  }

  .column {
    max-width: 300px;
    margin: 0;
  }
  
  .title {
    font-size: 32px;
    margin: 0 0 30px 0;
  }
  
  .message {
    max-width: 100%;
    padding: 20px 25px;
    font-size: 18px;
  }
  
  .chat-container {
    padding: 0 20px;
  }
  
  .chat-list {
    width: 95%;
  }
  
  .sent {
    max-width: 300px;
  }
  
  .received {
    margin-left: 35px;
  }
  
  .avatar {
    width: 28px;
    height: 28px;
    font-size: 14px;
  }
  
  .avatar-container {
    left: -35px;
  }
}

/* Make sure sequential messages from the same side have proper spacing */
.message + .message.received,
.message + .message.sent {
  margin-top: 8px; /* Reverted back to original 8px */
}
</style> 