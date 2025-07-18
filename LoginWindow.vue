<template>
  <v-app>
    <v-container
      fluid
      class="login-bg d-flex align-center justify-center"
    >
      <v-row class="fill-height" align="center" justify="center" no-gutters>
        <v-col cols="12" sm="10" md="6" lg="4">
          <v-card class="login-card" elevation="6">
            <v-window v-model="formMode" direction="horizontal-reverse">
              <!-- Login Window -->
              <v-window-item value="login">
                <v-img
                  src="https://images.unsplash.com/photo-1454496522488-7a8e488e8606"
                  height="60"
                  cover
                >
                  <div class="title-overlay">
                    <v-card-title class="white--text text-h5 font-weight-bold text-center w-100">
                      Login
                    </v-card-title>
                  </div>
                </v-img>
                <!-- Form -->
                <v-card-text class="card-content">
                  <!-- Login Method Selection -->
                  <v-btn-toggle
                    v-model="loginMethod"
                    mandatory
                    divided
                    color="primary"
                    size="small"
                    class="mb-3 w-100"
                  >
                    <v-btn value="email" class="flex-grow-1">
                      <v-icon left>mdi-email</v-icon>
                      Email
                    </v-btn>
                    <v-btn value="phone" class="flex-grow-1">
                      <v-icon left>mdi-phone</v-icon>
                      Phone
                    </v-btn>
                  </v-btn-toggle>

                  <v-form ref="loginForm" v-model="validForms.login">
                    <!-- Email/Username Login -->
                    <v-text-field
                      v-if="loginMethod === 'email'"
                      v-model="username"
                      label="Username or Email"
                      :rules="usernameRules"
                      prepend-icon="mdi-account"
                      outlined
                      dense
                      clearable
                      color="primary"
                      class="mb-3"
                    />
                    
                    <!-- Phone Number Login -->
                    <v-text-field
                      v-if="loginMethod === 'phone'"
                      v-model="phoneNumber"
                      label="Phone Number"
                      :rules="phoneRules"
                      prepend-icon="mdi-phone"
                      outlined
                      dense
                      clearable
                      color="primary"
                      class="mb-3"
                      placeholder="+1234567890"
                    />

                    <!-- Password for Email Login -->
                    <v-text-field
                      v-if="loginMethod === 'email'"
                      v-model="password"
                      :type="showPassword ? 'text' : 'password'"
                      label="Password"
                      :rules="passwordRules"
                      prepend-icon="mdi-lock"
                      :append-inner-icon="showPassword ? 'mdi-eye-off' : 'mdi-eye'"
                      @click:append-inner="showPassword = !showPassword"
                      outlined
                      dense
                      clearable
                      color="primary"
                      class="mb-3"
                    />

                    <!-- OTP for Phone Login -->
                    <v-otp-input
                      v-if="loginMethod === 'phone' && showPhoneOTP"
                      v-model="phoneOTP"
                      label="Enter 6-digit OTP"
                      :rules="[v => !!v || 'Please enter the 6-digit OTP']"
                      length="6"
                      type="number"
                      focused
                      focus-all
                      prepend-icon="mdi-shield-check"
                      class="mb-3"
                    />

                    <v-row v-if="loginMethod === 'email'" align="center" justify="space-between">
                      <v-checkbox v-model="rememberMe" label="Remember Me" hide-details dense />
                    </v-row>

                    <v-btn 
                      block 
                      color="green-darken-1" 
                      class="mt-3 py-1 font-weight-bold" 
                      size="small"
                      :loading="loading" 
                      :disabled="!validForms.login" 
                      @click="handleSubmit('login')"
                    >
                      {{ loginMethod === 'phone' && !showPhoneOTP ? 'Send OTP' : 'Login' }}
                    </v-btn>

                    <v-row v-if="loginMethod === 'email'" class="mt-2" justify="space-between">
                      <v-col cols="6">
                        <v-btn text class="text-caption" @click="formMode = 'forgotPassword'">
                          Forgot Password
                        </v-btn>
                      </v-col>
                      <v-col cols="6" class="text-right">
                        <v-btn text class="text-caption" @click="formMode = 'changePassword'">
                          Change Password
                        </v-btn>
                      </v-col>
                    </v-row>

                    <!-- Phone Login Back Button -->
                    <v-btn 
                      v-if="loginMethod === 'phone' && showPhoneOTP"
                      text 
                      color="blue-lighten-3" 
                      class="mt-2 text-caption w-100"
                      @click="showPhoneOTP = false; phoneOTP = ''"
                    >
                      Back to Phone Number
                    </v-btn>
                  </v-form>
                </v-card-text>
              </v-window-item>
              
              <!--Sign Up-->
              <v-window-item value="signup">
                <v-img src="https://images.unsplash.com/photo-1454496522488-7a8e488e8606" height="60" cover>
                  <div class="title-overlay">
                    <v-card-title class="white--text text-h5 font-weight-bold text-center w-100">
                      {{ showVerification ? 'Verify Account' : 'Sign Up' }}
                    </v-card-title>
                  </div>
                </v-img>
                <v-card-text class="card-content">
                  <!-- Signup Method Selection -->
                  <v-btn-toggle
                    v-if="!showVerification"
                    v-model="signupMethod"
                    mandatory
                    divided
                    color="primary"
                    size="small"
                    class="mb-3 w-100"
                  >
                    <v-btn value="email" class="flex-grow-1">
                      <v-icon left>mdi-email</v-icon>
                      Email
                    </v-btn>
                    <v-btn value="phone" class="flex-grow-1">
                      <v-icon left>mdi-phone</v-icon>
                      Phone
                    </v-btn>
                  </v-btn-toggle>

                  <v-form ref="signupForm" v-model="validForms.signup">
                    <!-- Email Signup Fields -->
                    <template v-if="signupMethod === 'email'">
                      <v-text-field
                        v-if="!showVerification"
                        v-model="username"
                        label="Username"
                        :rules="usernameRules"
                        prepend-icon="mdi-account"
                        outlined
                        dense
                        clearable
                        color="primary"
                        class="mb-3"
                      />
                      <v-text-field
                        v-if="!showVerification"
                        v-model="emailInput"
                        label="Email"
                        :rules="emailRules"
                        prepend-icon="mdi-email"
                        outlined
                        dense
                        clearable
                        color="primary"
                        class="mb-3"
                      />
                      <v-text-field
                        v-if="!showVerification"
                        v-model="password"
                        :type="showPassword ? 'text' : 'password'"
                        label="Password"
                        :rules="passwordRules"
                        prepend-icon="mdi-lock"
                        :append-inner-icon="showPassword ? 'mdi-eye-off' : 'mdi-eye'"
                        @click:append-inner="showPassword = !showPassword"
                        outlined
                        dense
                        clearable
                        color="primary"
                        class="mb-3"
                      />
                    </template>

                    <!-- Phone Signup Fields -->
                    <template v-if="signupMethod === 'phone'">
                      <v-text-field
                        v-if="!showVerification"
                        v-model="phoneNumber"
                        label="Phone Number"
                        :rules="phoneRules"
                        prepend-icon="mdi-phone"
                        outlined
                        dense
                        clearable
                        color="primary"
                        class="mb-3"
                        placeholder="+1234567890"
                      />
                      <v-text-field
                        v-if="!showVerification"
                        v-model="username"
                        label="Display Name (Optional)"
                        :rules="phoneUsernameRules"
                        prepend-icon="mdi-account"
                        outlined
                        dense
                        clearable
                        color="primary"
                        class="mb-3"
                        hint="This will be your display name"
                      />
                    </template>

                    <v-otp-input
                      v-if="showVerification"
                      v-model="verificationCode"
                      label="Verification Code"
                      :rules="[v => !!v || 'Please enter the 6-digit code we sent you']"
                      length="6"
                      type="number"
                      focused
                      focus-all
                      prepend-icon="mdi-shield-check"
                      class="mb-3"
                    />

                    <v-btn
                        block
                      color="green-darken-1"
                      class="mt-3 py-1 font-weight-bold"
                      size="small"
                      :loading="loading"
                      :disabled="!validForms.signup"
                      @click="handleSubmit('signup')"
                    >
                      {{ showVerification ? 'Verify & Complete Signup' : (signupMethod === 'phone' ? 'Send Verification Code' : 'Sign Up') }}
                    </v-btn>

                    <!-- Back button for phone verification -->
                    <v-btn 
                      v-if="showVerification && signupMethod === 'phone'"
                      text 
                      color="blue-lighten-3" 
                      class="mt-2 text-caption w-100"
                      @click="showVerification = false; verificationCode = ''"
                    >
                      Back to Phone Number
                    </v-btn>
                  </v-form>
                </v-card-text>
              </v-window-item>

              <!--Forgot Password-->
              <v-window-item value="forgotPassword">
                <v-img src="https://images.unsplash.com/photo-1454496522488-7a8e488e8606" height="60" cover>
                  <div class="title-overlay">
                    <v-card-title class="white--text text-h5 font-weight-bold text-center w-100">
                      Reset Password
                    </v-card-title>
                  </div>
                </v-img>
                <v-card-text class="card-content">
                  <v-form ref="forgotPasswordForm" v-model="validForms.forgotPassword">
                    <v-text-field
                      v-model="emailInput"
                      label="Email"
                      :rules="emailRules"
                      prepend-icon="mdi-email"
                      outlined
                      dense
                      clearable
                      color="primary"
                      class="mb-3"
                    />
                    <v-btn
                      block
                      color="green-darken-1"
                      class="mt-3 py-1 font-weight-bold"
                      size="small"
                      :loading="loading"
                      :disabled="!validForms.forgotPassword || resetCooldown"
                      @click="handleSubmit('forgotPassword')"
                    >
                      Send Reset Code
                    </v-btn>
                  </v-form>
                </v-card-text>
              </v-window-item>

              <v-window-item value="confirmResetPassword">
                <v-img 
                  src="https://images.unsplash.com/photo-1454496522488-7a8e488e8606"
                  height="60"
                  cover
                >
                  <div class="title-overlay">
                    <v-card-title class="white--text text-h5 font-weight-bold text-center w-100">
                      Set New Password
                    </v-card-title>
                  </div>
                </v-img>
                <v-card-text class="card-content">
                  <v-form ref="confirmResetPasswordForm" v-model="validForms.confirmResetPassword">
                    <v-text-field
                      v-model="emailInput"
                      label="Email"
                      :rules="emailRules"
                      prepend-icon="mdi-email"
                      outlined
                      dense
                      clearable
                      color="primary"
                      class="mb-3"
                    />
                    <v-text-field
                      v-model="password"
                      :type="showPassword ? 'text' : 'password'"
                      label="New Password"
                      :rules="passwordRules"
                      prepend-icon="mdi-lock"
                      :append-inner-icon="showPassword ? 'mdi-eye-off' : 'mdi-eye'"
                      @click:append-inner="showPassword = !showPassword"
                      outlined
                      dense
                      clearable
                      color="primary"
                      class="mb-3"
                    />
                    <v-otp-input
                      v-model="verificationCode"
                      label="Verification Code"
                      :rules="[v => !!v || 'Please enter the 6-digit code we sent you']"
                      length="6"
                      type="number"
                      focused
                      focus-all
                      prepend-icon="mdi-shield-check"
                      class="mb-3"
                    />
                    <v-btn
                      block
                      color="green-darken-1"
                      class="mt-3 py-1 font-weight-bold"
                      size="small"
                      :loading="loading"
                      :disabled="!validForms.confirmResetPassword"
                      @click="handleSubmit('confirmResetPassword')"
                    >
                      Set New Password
                    </v-btn>
                    <v-btn
                      text
                      color="blue-lighten-5"
                      class="mt-2 text-caption"
                      :disabled="loading || resetCooldown"
                      @click="handleResetPassword"
                    >
                      Resend Code
                    </v-btn>
                  </v-form>
                </v-card-text>
              </v-window-item>
              
              <!--CHANGE OF PASSWORD-->
              <v-window-item value="changePassword">
                <v-img
                  src="https://images.unsplash.com/photo-1454496522488-7a8e488e8606"
                  height="60"
                  cover
                >
                  <div class="title-overlay">
                    <v-card-title class="white--text text-h5 font-weight-bold text-center w-100">
                      Change Password
                    </v-card-title>
                  </div>
                </v-img>
                <v-card-text class="card-content">
                  <v-form ref="changePasswordForm" v-model="validForms.changePassword">
                    <v-text-field
                      v-model="oldPassword"
                      label="Old Password"
                      type="password"
                      :rules="passwordRules"
                      prepend-icon="mdi-lock"
                      outlined
                      dense
                      clearable
                      color="primary"
                      class="mb-3"
                    />
                    <v-text-field
                      v-model="password"
                      :type="showPassword ? 'text' : 'password'"
                      label="New Password"
                      :rules="passwordRules"
                      prepend-icon="mdi-lock"
                      :append-inner-icon="showPassword ? 'mdi-eye-off' : 'mdi-eye'"
                      @click:append-inner="showPassword = !showPassword"
                      outlined
                      dense
                      clearable
                      color="primary"
                      class="mb-3"
                    />
                    <v-btn
                      block
                      color="green-darken-1"
                      class="mt-3 py-1 font-weight-bold"
                      size="small"
                      :loading="loading"
                      :disabled="!validForms.changePassword"
                      @click="handleSubmit('changePassword')"
                    >
                      Change Password
                    </v-btn>
                  </v-form>
                </v-card-text>
              </v-window-item>
          </v-window>
            <!-- Footer -->
            <v-divider class="my-2"></v-divider>
            <v-card-text class="text-center footer-content">
              <span class="text-body-2">
                {{ formMode === 'login' ? "Don't have an account?" : 'Already have an account?' }}
                <v-btn
                  text
                  color="blue-lighten-3"
                  class="mt-2"
                  @click="formMode = formMode === 'login' ? 'signup' : 'login'"
                >
                  {{ formMode === 'login' ? 'Sign Up' : 'Login' }}
                </v-btn>
              </span>
            </v-card-text>

            <!-- Snackbar -->
            <v-snackbar
              v-model="snackbar.show"
              :color="snackbar.color"
              :timeout="3000"
              top
              right
            >
              {{ snackbar.message }}
              <template v-slot:action="{ attrs }">
                <v-btn color="white" text v-bind="attrs" @click="snackbar.show = false">Close</v-btn>
              </template>
            </v-snackbar>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
// Import AWS Amplify Auth methods
import { signIn, signUp, confirmSignUp, confirmSignIn, resetPassword, confirmResetPassword, updatePassword, signOut, getCurrentUser } from 'aws-amplify/auth';
import { onMounted } from 'vue';

export default {
  name: 'LoginWindow',
  data() {
    return {
      formMode: 'login',
      loginMethod: 'email', // 'email' or 'phone'
      signupMethod: 'email', // 'email' or 'phone'
      username: '',
      email: '',
      resetEmail: '',
      phoneNumber: '',
      phoneOTP: '',
      showPhoneOTP: false,
      password: '',
      oldPassword: '',
      verificationCode: '',
      rememberMe: false,
      showPassword: false,
      showVerification: false,
      loading: false,
      resetCooldown: false,
      // Hardcoded OTP for phone login (in production, this would be sent via SMS)
      HARDCODED_OTP: '123456',
      validForms: {
        login: false,
        signup: false,
        forgotPassword: false,
        confirmResetPassword: false,
        changePassword: false,
      },
      snackbar: {
        show: false,
        message: '',
        color: 'success',
      },
      usernameRules: [
        v => !!v || 'Username is required',
        v => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(v) || /^[a-zA-Z0-9._-]{3,}$/.test(v) || 'Enter a valid username or email',
      ],
      emailRules: [
        v => !!v || 'Email is required',
        v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
      ],
      phoneRules: [
        v => !!v || 'Phone number is required',
        v => /^\+?[1-9]\d{1,14}$/.test(v) || 'Enter a valid phone number with country code (e.g., +1234567890)',
      ],
      phoneUsernameRules: [
        // Optional validation for phone signup - less strict
        v => !v || v.length >= 2 || 'Display name must be at least 2 characters',
        v => !v || /^[a-zA-Z0-9._\s-]{2,}$/.test(v) || 'Display name can only contain letters, numbers, spaces, dots, underscores, and hyphens',
      ],
      passwordRules: [
        v => !!v || 'Password is required',
        v => v.length >= 8 || 'Minimum 8 characters',
        v => /[A-Z]/.test(v) || 'At least one uppercase letter',
        v => /[a-z]/.test(v) || 'At least one lowercase letter',
        v => /\d/.test(v) || 'At least one digit',
        v => /[@$!%*?&#^()_\-+=]/.test(v) || 'At least one special character',
      ],
    };
  },

  computed: {
    emailInput: {
      get() {
        return this.formMode === 'forgotPassword' || this.formMode === 'confirmResetPassword' ? this.resetEmail : this.email;
      },
      set(value) {
        if (this.formMode === 'forgotPassword' || this.formMode === 'confirmResetPassword') {
          this.resetEmail = value;
        } else {
          this.email = value;
        }
      },
    },
  },

  methods: {
    async handleSubmit(mode) {
      const formRef = `${mode}Form`;
      const { valid } = await this.$refs[formRef].validate();
      if (!valid) {
        this.snackbar.color = 'error';
        this.snackbar.message = 'Please fix the errors before proceeding.';
        this.snackbar.show = true;
        return;
      }

      this.loading = true;

      try {
        if (mode === 'login') {
          if (this.loginMethod === 'phone') {
            await this.handlePhoneLogin();
          } else {
            await this.login();
          }
        } else if (mode === 'signup') {
          if (!this.showVerification) {
            await this.signUp();
          } else {
            await this.confirmSignUp();
          }
        } else if (mode === 'forgotPassword') {
          await this.handleResetPassword();
        } else if (mode === 'confirmResetPassword') {
          await this.handleConfirmResetPassword();
        } else if (mode === 'changePassword') {
          await this.updateUserPassword();
        }
      } catch (error) {
        console.error(`${mode} error:`, error);
      } finally {
        this.loading = false;
      }
    },

    async handlePhoneLogin() {
      if (!this.showPhoneOTP) {
        // First step: Initiate phone login with AWS Amplify
        try {
          const { isSignedIn, nextStep } = await signIn({
            username: this.phoneNumber,
            options: {
              authFlowType: 'CUSTOM_WITH_SRP' // Use custom auth flow for phone
            }
          });

          if (isSignedIn) {
            this.snackbar.color = 'success';
            this.snackbar.message = 'Phone login successful!';
            this.snackbar.show = true;
            setTimeout(() => {
              this.$router.push('/dashboard');
            }, 1500);
          } else if (nextStep.signInStep === 'CONFIRM_SIGN_IN_WITH_SMS_MFA' || 
                     nextStep.signInStep === 'CONFIRM_SIGN_IN_WITH_CUSTOM_CHALLENGE') {
            this.showPhoneOTP = true;
            this.snackbar.color = 'info';
            this.snackbar.message = `Verification code sent to ${this.phoneNumber}`;
            this.snackbar.show = true;
          } else {
            // Fallback to hardcoded OTP for demo purposes
            this.showPhoneOTP = true;
            this.snackbar.color = 'info';
            this.snackbar.message = `OTP sent to ${this.phoneNumber}. For demo purposes, use: ${this.HARDCODED_OTP}`;
            this.snackbar.show = true;
          }
        } catch (error) {
          // If AWS auth fails, fallback to demo mode
          console.log('AWS phone auth not configured, using demo mode:', error);
          this.showPhoneOTP = true;
          this.snackbar.color = 'info';
          this.snackbar.message = `Demo mode: Use OTP ${this.HARDCODED_OTP} for ${this.phoneNumber}`;
          this.snackbar.show = true;
        }
        return;
      }

      // Second step: Verify OTP
      try {
        // Try AWS Amplify confirmation first
        const { isSignedIn } = await confirmSignIn({
          challengeResponse: this.phoneOTP
        });

        if (isSignedIn) {
          this.snackbar.color = 'success';
          this.snackbar.message = 'Phone login successful!';
          this.snackbar.show = true;
          setTimeout(() => {
            this.$router.push('/dashboard');
          }, 1500);
          return;
        }
      } catch (error) {
        console.log('AWS confirm sign in failed, trying demo mode:', error);
        
        // Fallback to hardcoded OTP verification
        if (this.phoneOTP === this.HARDCODED_OTP) {
          this.snackbar.color = 'success';
          this.snackbar.message = 'Phone login successful! (Demo mode)';
          this.snackbar.show = true;
          setTimeout(() => {
            this.$router.push('/dashboard');
          }, 1500);
          return;
        }
      }

      // If we reach here, OTP was invalid
      this.snackbar.color = 'error';
      this.snackbar.message = 'Invalid OTP. Please try again.';
      this.snackbar.show = true;
    },

    async login() {
      try {
        const { isSignedIn, nextStep } = await signIn({
          username: this.username,
          password: this.password,
        });
        
        if (isSignedIn) {
          this.snackbar.color = 'success';
          this.snackbar.message = 'Login Successful';
          this.snackbar.show = true;
          setTimeout(() => {
            this.$router.push('/dashboard');
          }, 1500);
        } else if (nextStep) {
          let loginMessage = 'Something went wrong with your login. Try again.';
          if (nextStep.signInStep === 'CONFIRM_SIGN_IN_WITH_SMS_MFA' || nextStep.signInStep === 'CONFIRM_SIGN_IN_WITH_TOTP_MFA') {
            loginMessage = 'We sent a code to verify your login.';
          } else if (nextStep.signInStep === 'RESET_PASSWORD') {
            loginMessage = 'Looks like you need to reset your password.';
          } else if (nextStep.signInStep === 'CONFIRM_SIGN_IN_WITH_NEW_PASSWORD_REQUIRED') {
            loginMessage = 'You need to set a new password. Follow the steps to continue.';
          } else if (nextStep.signInStep === 'CONFIRM_SIGN_UP') {
            loginMessage = 'Please verify your account first.';
          }
          this.snackbar.color = 'warning';
          this.snackbar.message = loginMessage;
          this.snackbar.show = true;
        }
      } catch (error) {
        this.snackbar.color = 'error';
        let errorMessage = 'Login failed. Please check your credentials.';
        
        if (error.name === 'NotAuthorizedException') {
          errorMessage = 'Incorrect username or password.';
        } else if (error.name === 'UserNotConfirmedException') {
          errorMessage = 'Please verify your account first.';
        } else if (error.name === 'UserNotFoundException') {
          errorMessage = 'User not found. Please check your username.';
        } else if (error.name === 'TooManyRequestsException') {
          errorMessage = 'Too many login attempts. Please try again later.';
        }
        
        this.snackbar.message = errorMessage;
        this.snackbar.show = true;
        console.error("Login error:", error);
      }
    },

    async signUp() {
      try {
        if (this.signupMethod === 'phone') {
          // Phone signup with AWS Amplify
          if (!this.showVerification) {
            // First step: Create account with phone number
            const { isSignUpComplete, userId, nextStep } = await signUp({
              username: this.phoneNumber, // Use phone as username
              password: this.generateTempPassword(), // Generate temporary password for phone users
              options: {
                userAttributes: {
                  phone_number: this.phoneNumber, // E.164 format
                  name: this.username || 'Phone User', // Use provided name or default
                },
                autoSignIn: false // We'll handle sign in separately for phone users
              }
            });

            console.log('Phone signup userId:', userId);
            
            if (nextStep.signUpStep === 'CONFIRM_SIGN_UP') {
              this.showVerification = true;
              this.snackbar.color = 'success';
              this.snackbar.message = `Verification code sent to ${this.phoneNumber}`;
              this.snackbar.show = true;
            } else if (isSignUpComplete) {
              this.snackbar.color = 'success';
              this.snackbar.message = 'Phone signup completed successfully!';
              this.snackbar.show = true;
              setTimeout(() => {
                this.formMode = 'login';
                this.loginMethod = 'phone';
              }, 2000);
            }
          } else {
            // Second step: Confirm phone signup
            await this.confirmSignUp();
          }
        } else {
          // Email signup with AWS Amplify
          const { isSignUpComplete, userId, nextStep } = await signUp({
            username: this.email,
            password: this.password,
            options: {
              userAttributes: {
                email: this.email,
                name: this.username,
              },
              autoSignIn: true
            }
          });

          console.log('Email signup userId:', userId);
          
          if (nextStep.signUpStep === 'CONFIRM_SIGN_UP') {
            this.showVerification = true;
            this.snackbar.color = 'success';
            this.snackbar.message = 'Please enter the verification code sent to your email.';
            this.snackbar.show = true;
          } else if (isSignUpComplete) {
            this.snackbar.color = 'success';
            this.snackbar.message = 'Sign-up completed successfully!';
            this.snackbar.show = true;
            setTimeout(() => {
              this.$router.push('/dashboard');
            }, 1500);
          }
        }
      } catch (error) {
        this.snackbar.color = 'error';
        let errorMessage = 'Sign-up failed. Please try again.';
        
        if (error.name === 'UsernameExistsException') {
          errorMessage = this.signupMethod === 'phone' ? 'This phone number is already registered.' : 'This email is already registered.';
        } else if (error.name === 'InvalidPasswordException') {
          errorMessage = 'Password does not meet the requirements.';
        } else if (error.name === 'InvalidParameterException') {
          errorMessage = 'Invalid input. Please check your information.';
        }
        
        this.snackbar.message = errorMessage;
        this.snackbar.show = true;
        console.error("Sign-up error:", error);
      }
    },

    async confirmSignUp() {
      try {
        const username = this.signupMethod === 'phone' ? this.phoneNumber : this.email;
        
        const { isSignUpComplete } = await confirmSignUp({
          username: username,
          confirmationCode: this.verificationCode,
        });
        
        if (isSignUpComplete) {
          this.snackbar.color = 'success';
          this.snackbar.message = this.signupMethod === 'phone' ? 'Phone number verified successfully' : 'Email verified successfully';
          this.snackbar.show = true;
          this.showVerification = false;
          
          if (this.signupMethod === 'phone') {
            // For phone users, redirect to phone login
            setTimeout(() => {
              this.formMode = 'login';
              this.loginMethod = 'phone';
            }, 1500);
          } else {
            // For email users, they might be auto-signed in, so sign out first
            try {
              await signOut();
            } catch (signOutError) {
              // User might not be signed in, that's okay
              console.log('No user to sign out');
            }
            setTimeout(() => {
              this.formMode = 'login';
              this.loginMethod = 'email';
            }, 1500);
          }
        } else {
          this.snackbar.color = 'error';
          this.snackbar.message = 'Verification failed. Check your code.';
          this.snackbar.show = true;
        }
      } catch (error) {
        this.snackbar.color = 'error';
        let errorMessage = 'Verification failed. Please try again.';
        
        if (error.name === 'CodeMismatchException') {
          errorMessage = 'Invalid verification code.';
        } else if (error.name === 'ExpiredCodeException') {
          errorMessage = 'Verification code has expired.';
        } else if (error.name === 'UserNotFoundException') {
          errorMessage = 'User not found.';
        }
        
        this.snackbar.message = errorMessage;
        this.snackbar.show = true;
        console.error("Confirmation error:", error);
      }
    },

    generateTempPassword() {
      // Generate a temporary password for phone users that meets AWS Cognito requirements
      const length = 12;
      const charset = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789@#$%";
      let password = "";
      
      // Ensure we have at least one of each required character type
      password += "A"; // Uppercase
      password += "a"; // Lowercase  
      password += "1"; // Number
      password += "@"; // Special character
      
      // Fill the rest randomly
      for (let i = password.length; i < length; i++) {
        password += charset.charAt(Math.floor(Math.random() * charset.length));
      }
      
      // Shuffle the password
      return password.split('').sort(() => 0.5 - Math.random()).join('');
    },

    async handleResetPassword() {
      if (this.resetCooldown) {
        this.snackbar.color = 'error';
        this.snackbar.message = 'Please wait before requesting another code.';
        this.snackbar.show = true;
        return;
      }
      try {
        const output = await resetPassword({ username: this.resetEmail });
        this.handleResetPasswordNextSteps(output);
        this.resetCooldown = true;
        setTimeout(() => {
          this.resetCooldown = false;
        }, 30000);
      } catch (error) {
        let errorMessage = 'Something went wrong sending the reset code.';
        if (error.name === 'UserNotFoundException') {
          errorMessage = 'No account found with this email.';
        } else if (error.name === 'LimitExceededException') {
          errorMessage = 'Too many reset attempts. Try again later.';
        } else if (error.name === 'CodeDeliveryFailureException') {
          errorMessage = 'Failed to send reset code. Check your email setup.';
        } else if (error.name === 'InvalidParameterException') {
          errorMessage = 'Invalid email format.';
        }
        this.snackbar.color = 'error';
        this.snackbar.message = errorMessage;
        this.snackbar.show = true;
      }
    },

    async handleResetPasswordNextSteps(output) {
      const { nextStep } = output;
      switch (nextStep.resetPasswordStep) {
        case 'CONFIRM_RESET_PASSWORD_WITH_CODE':
          this.snackbar.color = 'success';
          this.snackbar.message = `We sent a code to ${nextStep.codeDeliveryDetails.destination}. Check your email!`;
          this.snackbar.show = true;
          this.formMode = 'confirmResetPassword';
          break;
        case 'DONE':
          this.snackbar.color = 'success';
          this.snackbar.message = 'Password reset complete! Log in with your new password.';
          this.snackbar.show = true;
          setTimeout(() => {
            this.formMode = 'login';
          }, 2000);
          break;
      }
    },

    async handleConfirmResetPassword() {
      try {
        await confirmResetPassword({
          username: this.resetEmail,
          confirmationCode: this.verificationCode,
          newPassword: this.password,
        });
        this.snackbar.color = 'success';
        this.snackbar.message = 'Password reset completed.';
        this.snackbar.show = true;
        this.formMode = 'login';
      } catch (error) {
        let errorMessage = 'Failed to reset password. Please try again.';
        if (error.name === 'CodeMismatchException') {
          errorMessage = 'Invalid verification code.';
        } else if (error.name === 'ExpiredCodeException') {
          errorMessage = 'The code has expired.';
        } else if (error.name === 'InvalidPasswordException') {
          errorMessage = 'New password must be at least 8 characters.';
        } else if (error.name === 'UserNotFoundException') {
          errorMessage = 'No account found with that email.';
        }
        this.snackbar.color = 'error';
        this.snackbar.message = errorMessage;
        this.snackbar.show = true;
      }
    },

    async updateUserPassword() {
      try {
        await updatePassword({
          oldPassword: this.oldPassword,
          newPassword: this.password,
        });
        this.snackbar.color = 'success';
        this.snackbar.message = 'Password changed successfully';
        this.snackbar.show = true;
        this.formMode = 'login';
      } catch (error) {
        let errorMessage = 'Failed to change password.';
        if (error.name === 'NotAuthorizedException') {
          errorMessage = 'Old password is incorrect.';
        } else if (error.name === 'InvalidPasswordException') {
          errorMessage = 'New password does not meet requirements.';
        }
        this.snackbar.color = 'error';
        this.snackbar.message = errorMessage;
        this.snackbar.show = true;
      }
    },

    resetForm(mode) {
      const formRef = `${mode}Form`;
      if (this.$refs[formRef]) {
        this.$refs[formRef].reset();
      }
      this.username = '';
      this.email = '';
      this.resetEmail = '';
      this.phoneNumber = '';
      this.phoneOTP = '';
      this.showPhoneOTP = false;
      this.password = '';
      this.oldPassword = '';
      this.verificationCode = '';
      this.showVerification = false;
      this.resetCooldown = false;
      this.snackbar.show = false;
    },

    async mounted() {
      try {
        const user = await getCurrentUser();
        console.log('Logged in user email:', user.signInDetails?.loginId);
        this.loggedInEmail = user.signInDetails?.loginId;
      } catch (err) {
        console.error('No user logged in');
      }
    },
  },

  watch: {
    formMode(newMode, oldMode) {
      if (newMode !== oldMode) {
        this.resetForm(newMode);
      }
    },
    loginMethod() {
      this.showPhoneOTP = false;
      this.phoneOTP = '';
    },
    signupMethod() {
      this.showVerification = false;
      this.verificationCode = '';
    },
  },
};
</script>

<style scoped>
.login-bg {
  background: url('https://images.unsplash.com/photo-1501785888041-af3ef285b470') no-repeat center center fixed;
  background-size: cover;
  min-height: 100vh;
  padding: 0;
  margin: 0;
}
.title-overlay {
  background: rgba(0, 0, 0, 0.4);
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}
.login-card {
  border-radius: 16px;
  overflow: hidden;
  background-color: whitesmoke;
  backdrop-filter: blur(3px);
}
html,
body {
  margin: 0;
  padding: 0;
  height: 100%;
  overflow: hidden;
}

.w-100 {
  width: 100%;
}

/* Compact design styles */
.card-content {
  padding: 12px 16px !important;
}

.v-text-field {
  margin-bottom: 8px !important;
}

.v-btn-toggle {
  height: 32px !important;
}

.v-btn-toggle .v-btn {
  font-size: 0.875rem !important;
  padding: 0 12px !important;
}

.login-card {
  max-width: 400px;
  margin: 0 auto;
}

.v-otp-input {
  margin-bottom: 8px !important;
}
</style>