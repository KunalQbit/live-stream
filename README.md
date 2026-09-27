# live-stream  
# Real-Time Public Chat & Floating Poll Web App

A polished, production-quality, single-page web application featuring a modern real-time public chat room, `@mention` autocomplete, user presence tracking, and an advanced timestamp-based temporary poll system with floating notification islands. Built with HTML5, Tailwind CSS, Vanilla JavaScript, and Firebase Realtime Database.

---

## Features

1. **Anonymous / Temporary User System**: Welcome modal prompting for a display name, stored locally with a secure unique client-side ID (`user_...`).
2. **Real-Time Public Chat**: Instant message synchronization, avatars, smart timestamps, auto-scroll with unread indicator, and sanitization against XSS.
3. **Mention & Tag System**: Type `@` to open an autocomplete popup of online users; tags are visually highlighted.
4. **User Presence**: Real-time online user count backed by Firebase `.info/connected` and `onDisconnect()`.
5. **Advanced Poll Lifecycle**:
   - Create polls with 2 to 6 options.
   - Initial 30-second display in chat.
   - Automatic transition into a floating poll notification stack at the top of the screen.
   - 10-minute timestamp-based expiration.
6. **Real-Time Voting**: Click any floating poll to open the vote modal; percentages and progress bars update instantly across all connected users without refreshing.
7. **Responsive & Accessible**: Fully optimized for mobile safe areas, tablets, and desktops with a built-in Dark/Light theme toggle.

---

## Firebase Setup & Configuration

1. Go to the [Firebase Console](https://console.firebase.google.com/).
2. Create a new project or use your existing project (`yt-live-stream-4e4ea`).
3. Enable **Realtime Database** in test mode or production mode.
4. The Firebase configuration is already pre-configured in `index.html` via CDN.

### Recommended Firebase Realtime Database Security Rules

In your Firebase Console under **Realtime Database -> Rules**, apply the following rules to secure your data while allowing public chat and polling:

```json
{
  "rules": {
    "messages": {
      "$messageId": {
        ".write": "data.notExists() || newData.child('userId').val() === auth.uid",
        ".validate": "newData.hasChildren(['userId', 'username', 'text', 'createdAt']) && newData.child('text').val().length <= 500 && newData.child('username').val().length <= 24"
      }
    },
    "presence": {
      "$userId": {
        ".write": true,
        ".validate": "newData.hasChildren(['name', 'lastSeen'])"
      }
    },
    "typing": {
      "$userId": {
        ".write": true
      }
    },
    "polls": {
      "$pollId": {
        ".write": "data.notExists() || newData.child('creatorId').val() === auth.uid || data.child('votes').exists()",
        ".validate": "newData.hasChildren(['question', 'options', 'createdAt', 'expiresAt', 'floatingAt'])"
      }
    }
  }
}
