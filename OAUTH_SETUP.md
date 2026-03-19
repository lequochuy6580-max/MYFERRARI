# 🔐 Social Authentication Setup Guide

## Overview
This document explains how to set up Google, Facebook, and GitHub OAuth integration for the Ferrari E-Shop login system.

## 🚀 Quick Start (Development/Testing)

The system currently uses **simulated social login** for development. When you click a social login button, it will prompt you for name and email, then create/login the user.

**No setup required** - it works out of the box! ✅

### Test Credentials (Simulated):
- **Google**: Any name, example@gmail.com
- **Facebook**: Any name, example@facebook.com
- **GitHub**: Any name, example@github.com

---

## 📋 Production Setup

To enable real OAuth authentication, follow these steps:

### 1️⃣ Google OAuth Setup

#### Get Google Credentials:
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing
3. Enable Google+ API
4. Go to "Credentials" → Create OAuth 2.0 Client ID
5. Choose "Web application"
6. Add authorized redirect URIs:
   ```
   http://localhost:3000/login.html
   https://yourdomain.com/login.html
   ```
7. Copy Client ID

#### Update Code:
In `src/socialAuth.ts`:
```typescript
private config: OAuthConfig = {
  googleClientId: "YOUR_GOOGLE_CLIENT_ID_HERE",
  // ... rest of config
};
```

#### Update HTML:
The Google SDK script is already included in `login.html`

---

### 2️⃣ Facebook OAuth Setup

#### Get Facebook Credentials:
1. Go to [Facebook Developers](https://developers.facebook.com/)
2. Create a new app or use existing
3. Add "Facebook Login" product
4. Configure in Settings:
   - App Domain: `yourdomain.com`
   - Valid OAuth Redirect URIs:
     ```
     http://localhost:3000/login.html
     https://yourdomain.com/login.html
     ```
5. Copy App ID

#### Update Code:
In `src/socialAuth.ts`:
```typescript
private config: OAuthConfig = {
  facebookAppId: "YOUR_FACEBOOK_APP_ID_HERE",
  // ... rest of config
};
```

#### Update HTML (`login.html`):
Replace `YOUR_FACEBOOK_APP_ID` in the Facebook SDK script tag:
```html
src="https://connect.facebook.net/vi_VN/sdk.js#xfbml=1&version=v18.0&appId=YOUR_FACEBOOK_APP_ID"
```

---

### 3️⃣ GitHub OAuth Setup

#### Get GitHub Credentials:
1. Go to GitHub Settings → Developer Settings → OAuth Apps
2. Create New OAuth App with:
   - Application Name: Ferrari E-Shop
   - Homepage URL: `https://yourdomain.com`
   - Authorization callback URL: `https://yourdomain.com/login.html`
3. Copy Client ID and Client Secret

#### Update Code:
In `src/socialAuth.ts`:
```typescript
private config: OAuthConfig = {
  githubClientId: "YOUR_GITHUB_CLIENT_ID_HERE",
  // ... rest of config
};
```

#### Backend Implementation:
For GitHub, you need a backend endpoint to exchange the auth code for a token:
- Endpoint: `/api/auth/github/callback`
- Exchange code for token using Client Secret
- Return user info

---

## 📂 File Structure

```
src/
├── auth.ts                 # Core auth system
├── login.ts               # Login functionality
├── signup.ts              # Signup functionality
├── socialAuth.ts          # OAuth integration (NEW)
└── app.ts                 # Module bootstrapper

dist/
├── auth.js                # Compiled auth
├── login.js              # Compiled login
├── signup.js             # Compiled signup
├── socialAuth.js         # Compiled OAuth (NEW)
└── app.js                # Compiled app

login.html                  # Login page (updated with OAuth SDKs)
```

---

## 🔧 Configuration

### Development (Current):
```typescript
// Simulated social login - works without setup
// Prompts user for name and email, then creates account
```

### Production:
```typescript
// Update socialAuth.ts with real credentials
const config: OAuthConfig = {
  googleClientId: "xxx.apps.googleusercontent.com",
  facebookAppId: "123456789",
  githubClientId: "abc123def456",
  redirectUri: "https://yourdomain.com/login.html"
};
```

---

## 🎯 OAuth Flow

### Current (Development - Simulated):
```
User clicks "Google" → Prompt for name/email → Create account → Redirect to home
```

### Production (Real OAuth):

#### Google:
```
User clicks "Google" → Google Login Screen → User authorizes → 
JWT token received → Decode user info → Create/login account → Redirect
```

#### Facebook:
```
User clicks "Facebook" → Facebook Login Screen → User authorizes → 
Access token received → Get user info via Graph API → Create/login account → Redirect
```

#### GitHub:
```
User clicks "GitHub" → GitHub Login Screen → User authorizes → 
Authorization code received → Backend exchanges for token → 
Get user info → Create/login account → Redirect
```

---

## 🛡️ Security Considerations

### Current Implementation (Development):
⚠️ **Not Production-Ready**
- Simple password hashing (not bcrypt)
- Passwords stored in localStorage
- No server-side validation

### Production Recommendations:
✅ **Required**
- Use HTTPS only
- Implement server-side OAuth token validation
- Store tokens securely with HttpOnly cookies
- Implement rate limiting on auth endpoints
- Add CSRF token validation
- Use bcrypt for password hashing
- Store sensitive data on server only

---

## 🧪 Testing

### Test Social Login:
1. Open `login.html`
2. Click social login button (Google/Facebook/GitHub)
3. For development: Enter test name and email
4. Should create new user and redirect to home

### Test Login Persistence:
1. Login with social account
2. Refresh page - user should still be logged in
3. Check header shows user name

### Test User in Admin:
1. Login via social account
2. Go to Admin → Customers
3. Should see new user with provider (Google/Facebook/GitHub)

---

## 📞 Troubleshooting

### "SDK not loaded" message
- Check browser console for errors
- Verify SDK script tags are in `login.html`
- Check network tab for SDK load failures

### Social login not working
- Verify credentials are set correctly in `socialAuth.ts`
- Check OAuth app configuration (redirect URIs)
- Verify app domain is whitelisted

### User not appearing in Admin
- Check localStorage for `ferrariUsers` key
- Verify user data structure matches expected format
- Check Admin_Customers.ts loads from `ferrariUsers`

### OAuth redirect failing
- Verify redirect URI matches in OAuth app settings
- Check redirect URI doesn't have trailing slash mismatch
- Test with `http://localhost:3000` first

---

## 🚀 Next Steps

1. **For Development**: Use as-is, simulated login works
2. **For Staging**: Get OAuth test credentials from providers
3. **For Production**:
   - Get OAuth production credentials
   - Implement backend OAuth endpoints
   - Update security (HTTPS, HttpOnly cookies, etc.)
   - Set up environment variables for secrets
   - Test with real credentials

---

## 📚 References

- [Google OAuth 2.0](https://developers.google.com/identity/protocols/oauth2)
- [Facebook Login Documentation](https://developers.facebook.com/docs/facebook-login)
- [GitHub OAuth Documentation](https://docs.github.com/en/developers/apps/building-oauth-apps)
- [TypeScript Type Safety](https://www.typescriptlang.org/)

---

## 💡 Features

✅ **Google Login** - One-click authentication  
✅ **Facebook Login** - Social sharing friendly  
✅ **GitHub Login** - Developer-friendly  
✅ **Simulated Mode** - Works without setup  
✅ **Real OAuth** - Production ready with credentials  
✅ **Type Safe** - Full TypeScript support  
✅ **Error Handling** - Graceful fallbacks  
✅ **User Tracking** - All providers tracked in Admin  

---

## 📝 Notes

- OAuth setup is optional - simulated login works for development
- All OAuth integrations use same user database
- User provider is stored for future reference
- Social login creates automatic accounts
- No email verification needed for simulated login
