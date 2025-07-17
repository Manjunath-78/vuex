# AWS Cognito Authentication Troubleshooting Guide

## Common Causes of "NotAuthorizedException: Incorrect username or password"

### 1. **Username vs Email Configuration Issue**

**Problem**: Your Cognito User Pool might be configured differently than expected.

**Check Your Cognito Configuration**:
1. Go to AWS Console → Cognito → User Pools
2. Select your User Pool
3. Check "Sign-in experience" tab
4. Look at "Cognito user pool sign-in options"

**Common Configurations**:
- **Email only**: Users sign up with email, must login with email
- **Username only**: Users sign up with username, must login with username  
- **Email and Username**: Users can login with either

### 2. **User Account Issues**

**Check if user exists and is confirmed**:
```javascript
// Add this debug function to your component
async debugUser() {
  try {
    // Check current auth state
    const user = await getCurrentUser();
    console.log('Current user:', user);
  } catch (error) {
    console.log('No current user');
  }
}
```

### 3. **Registration vs Login Mismatch**

**Your current signup process**:
```javascript
await signUp({
  username: this.email,  // ← Using email as username
  password: this.password,
  options: {
    userAttributes: {
      email: this.email,
      preferred_username: this.signupUsername, // ← This is just an attribute
    }
  }
});
```

**This means**:
- The actual "username" in Cognito is the EMAIL
- The `preferred_username` is just an attribute, not for login
- You must login with the EMAIL, not the display username

## 🔧 **SOLUTIONS**

### Solution 1: Login with Email Only (Recommended)

Update your login to always use email:

```javascript
async login() {
  try {
    const { isSignedIn, nextStep } = await signIn({
      username: this.username, // This should be the email used during signup
      password: this.password,
    });
    // ... rest of login logic
  } catch (error) {
    console.error('Login error:', error);
  }
}
```

### Solution 2: Update Your Signup to Use Username

If you want username login, change your signup:

```javascript
async signUp() {
  try {
    const { nextStep } = await signUp({
      username: this.signupUsername, // ← Use actual username
      password: this.password,
      options: {
        userAttributes: {
          email: this.email,
        },
      },
    });
    // ... rest of signup logic
  } catch (error) {
    console.error('Sign-up error:', error);
  }
}
```

### Solution 3: Add User Lookup (Advanced)

Create a system to map usernames to emails:

```javascript
// This would require additional backend logic
async loginWithUsernameOrEmail() {
  const isEmail = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(this.username);
  
  let loginUsername = this.username;
  
  if (!isEmail) {
    // If it's not an email, you'd need to look up the email by username
    // This requires custom backend logic or DynamoDB table
    loginUsername = await this.lookupEmailByUsername(this.username);
  }
  
  const { isSignedIn } = await signIn({
    username: loginUsername,
    password: this.password,
  });
}
```

## 🔍 **DEBUGGING STEPS**

### Step 1: Check Your User Pool Configuration
1. AWS Console → Cognito → User Pools → Your Pool
2. "Sign-in experience" → Check what's allowed for sign-in

### Step 2: Check Existing Users  
1. AWS Console → Cognito → User Pools → Your Pool → Users
2. Look at the "Username" column - this is what you use to login
3. Check if users are "Confirmed"

### Step 3: Test with Known Email
Try logging in with the exact email address used during registration.

### Step 4: Check Console Logs
The updated code will show you:
```javascript
console.log('Login attempt with:', {
  username: this.username,
  isEmail: isEmail,
  inputType: isEmail ? 'email' : 'username'
});
```

## 🎯 **MOST LIKELY FIX**

Based on your signup code, you're using **email as username**. Therefore:

1. **Users must login with their EMAIL address, not username**
2. Update your form label to be clearer:
   ```html
   <v-text-field
     v-model="username"
     label="Email Address" <!-- ← Change this -->
     :rules="emailRules"     <!-- ← Use email rules -->
   />
   ```

3. **Or** ask users for both during login and handle appropriately

## 📝 **Quick Test**

1. Go to AWS Cognito Console
2. Find a test user 
3. Note the exact "Username" value
4. Try logging in with that exact value
5. If it works, the issue is username/email mapping

The error suggests the credentials don't match. Most likely, you need to use the email address that was used during signup as the login username.