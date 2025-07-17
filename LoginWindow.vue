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
              <v-window-item value="login">
                <v-img
                  src="https://images.unsplash.com/photo-1454496522488-7a8e488e8606"
                  height="92"
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
                  <v-form ref="loginForm" v-model="validForms.login">
                    <v-text-field
                      v-model="username"
                      label="Email Address"
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
                    <v-row align="center" justify="space-between">
                      <v-checkbox v-model="rememberMe" label="Remember Me" hide-details dense />
                    </v-row>
                    <v-btn block color="green-darken-1" class="mt-4 py-2 font-weight-bold" :loading="loading" :disabled="!validForms.login" @click="handleSubmit('login')">
                      Login
                    </v-btn>
                    <v-row class="mt-2" justify="space-between">
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
                  </v-form>
                </v-card-text>
              </v-window-item>
              
              <!--Sign Up-->
              <v-window-item value="signup">
                <v-img src="https://images.unsplash.com/photo-1454496522488-7a8e488e8606" height="90" cover>
                  <div class="title-overlay">
                    <v-card-title class="white--text text-h5 font-weight-bold text-center w-100">
                      {{ showVerification ? 'Verify Account' : 'Sign Up' }}
                    </v-card-title>
                  </div>
                </v-img>
                <v-card-text class="card-content">
                  <v-form ref="signupForm" v-model="validForms.signup">
                    <v-text-field
                      v-if="!showVerification"
                      v-model="signupUsername"
                      label="Username"
                      :rules="signupUsernameRules"
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
                      class="mt-4 py-2 font-weight-bold"
                      :loading="loading"
                      :disabled="!validForms.signup"
                      @click="handleSubmit('signup')"
                    >
                      {{ showVerification ? 'Confirm Sign Up' : 'Sign Up' }}
                    </v-btn>
                  </v-form>
                </v-card-text>
              </v-window-item>

              <!--Forgot Password-->
              <v-window-item value="forgotPassword">
                <v-img src="https://images.unsplash.com/photo-1454496522488-7a8e488e8606" height="140" cover>
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
                      class="mt-4 py-2 font-weight-bold"
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
                  height="140"
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
                      class="mt-4 py-2 font-weight-bold"
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
                  height="140"
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
                      class="mt-4 py-2 font-weight-bold"
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
import { signIn, signUp, confirmSignUp, resetPassword, confirmResetPassword, updatePassword, signOut } from 'aws-amplify/auth';

// This is the main part of logic for all 
export default {
  name: 'LoginWindow',
  data() {
    return {
      formMode: 'login', 
      username: '',
      signupUsername: '',
      email: '',
      resetEmail: '',
      password: '',
      oldPassword: '',
      verificationCode: '',
      rememberMe: false,
      showPassword: false,
      showVerification: false,
      loading: false,
      resetCooldown: false,
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
        timeout: 2000,
      },
      // Fixed validation rules - allow both username and email for login
      usernameRules: [
        v => !!v || 'Username or Email is required',
        v => v.length >= 3 || 'Username must be at least 3 characters',
      ],
      // Separate rules for signup username (more strict)
      signupUsernameRules: [
        v => !!v || 'Username is required',
        v => /^[a-zA-Z0-9._-]{3,}$/.test(v) || 'Username must be at least 3 characters and contain only letters, numbers, dots, underscores, or hyphens',
      ],
      emailRules: [
        v => !!v || 'Email is required',
        v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
      ],
      passwordRules: [
        v => !!v || 'Password is required',
        v => v.length >= 8 || 'Minimum 8 characters',
        v => /[A-Z]/.test(v) || 'At least one uppercase letter',
        v => /[a-z]/.test(v) || 'At least one lowercase letter',
        v => /\d/.test(v) || 'At least one digit',
        v => /[@$!%*?&#^()_\-+=]/.test(v) || 'At least one special character',
      ],
    }
  },

  computed: {
    // It manages both sign up and password reset  
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
      const { valid } = await this.$refs[formRef].validate()
      if (!valid) {
        this.snackbar.color = 'error'
        this.snackbar.message = 'Please fix the errors before submitting.'
        this.snackbar.show = true
        return;
      }

      this.loading = true;
      try {
        if (mode === 'login') {
          await this.login();
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
        } else if  (mode === 'changePassword') {
          await this.updateUserPassword();
        }
      } catch (error) {
        console.error(`${mode} error:`, error);
      } finally {
        this.loading = false;
      }
    },  

    async login() {
      try {
        // Check if input looks like an email
        const isEmail = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(this.username);
        
        // For debugging - you can remove these console.logs later
        console.log('Login attempt with:', {
          username: this.username,
          isEmail: isEmail,
          inputType: isEmail ? 'email' : 'username'
        });

        const { isSignedIn, nextStep } = await signIn({
          username: this.username, // AWS Amplify will handle both username and email
          password: this.password,
        });

        if (isSignedIn) {
          this.snackbar.color = 'success';
          this.snackbar.message = 'Login Successful';
          this.snackbar.show = true;
          setTimeout(() => {
            this.$router.push('/dashboard');
          }, 1500)
        } else {
          let loginMessage = 'Something not right with your login. Try again.';
          if (nextStep.signInStep === 'CONFIRM_SIGN_IN_WITH_SMS_MFA' || nextStep.signInStep === 'CONFIRM_SIGN_IN_WITH_TOTP_MFA') {
            loginMessage = 'We sent a code to verify your login.';
          } else if (nextStep.signInStep === 'RESET_PASSWORD') {
            loginMessage = 'Looks like you need to reset your password.';
          } else if (nextStep.signInStep === 'CONFIRM_SIGN_IN_WITH_NEW_PASSWORD_REQUIRED') {
            loginMessage = 'You need to set a new password. Follow the steps to continue.';
          }
          this.snackbar.color = 'error';
          this.snackbar.message = loginMessage;
          this.snackbar.show = true;
        }
      } catch (error) {
        console.error('Login error:', error);
        
        // More specific error handling
        let errorMessage = 'Login failed. Please check your credentials.';
        
        if (error.name === 'NotAuthorizedException') {
          if (error.message.includes('Incorrect username or password')) {
            errorMessage = 'Incorrect username/email or password. Please check your credentials.';
          } else if (error.message.includes('User is not confirmed')) {
            errorMessage = 'Please verify your email address before logging in.';
          } else if (error.message.includes('Password attempts exceeded')) {
            errorMessage = 'Too many failed attempts. Please try again later.';
          }
        } else if (error.name === 'UserNotFoundException') {
          errorMessage = 'User not found. Please check your username/email or sign up.';
        } else if (error.name === 'UserNotConfirmedException') {
          errorMessage = 'Please verify your email address first.';
        }
        
        this.snackbar.color = 'error';
        this.snackbar.message = errorMessage;
        this.snackbar.show = true;
      }   
    },

    // Signs up a new user in the AWS
    async signUp() {
      try {
        const { nextStep } = await signUp({
          username: this.email,
          password: this.password,
          options: {
            userAttributes: {
              email: this.email,
              preferred_username: this.signupUsername,
            },
            autoSignIn: true,
          },
        });
        if(nextStep.signUpStep === 'CONFIRM_SIGN_UP') {
          this.showVerification = true;
          this.snackbar.color = 'success';
          this.snackbar.message = 'Please enter the verification code sent to your email.';
          this.snackbar.show = true;
        } else {
          this.snackbar.color = 'success';
          this.snackbar.message = 'Sign-up successful';
          this.snackbar.show = true;
        }
      } catch (error) {
        console.error('Sign-up error:', error);
        this.snackbar.color = 'error';
        this.snackbar.message = 'Sign-Up failed. Please try again.';
        this.snackbar.show = true;
      }
    },

    // Confirm sign up with verification code
    async confirmSignUp() {
      try {
        const { isSignUpComplete } = await confirmSignUp({
          username: this.email, // Use email as username for confirmation
          confirmationCode: this.verificationCode,
        })
        if (isSignUpComplete) {
          this.snackbar.color = 'success';
          this.snackbar.message = 'Account verified successfully';
          this.snackbar.show = true;
          this.showVerification = false;
          await signOut();

          setTimeout(() => {
            this.formMode = 'login';
          }, 1500);
        } else {
          this.snackbar.color = 'error';
          this.snackbar.message = 'Verification did not work. Check your code.';
          this.snackbar.show = true;
        }
      } catch (error) {
        console.error('Verification error:', error);
        this.snackbar.color = 'error';
        this.snackbar.message = 'Verification failed. Please try again.';
        this.snackbar.show = true;
      }
    },

    // Password reset
    async handleResetPassword() {
      if (this.resetCooldown) {
        this.snackbar.color = 'error';
        this.snackbar.message = 'Please wait a bit before requesting another code.';
        this.snackbar.show = true;
        return;
      }
      try {
        const output = await resetPassword({ username: this.resetEmail});
        this.handleResetPasswordNextSteps(output);
        this.resetCooldown = true;
        setTimeout(() => {
          this.resetCooldown = false;
        }, 30000);
      } catch (error) {
        console.error('Reset Password error:', error);
        console.log('Error details:', JSON.stringify(error, null, 2));
        let errorMessage = 'Something went wrong sending the reset code.';
        if (error.name === 'UserNotFoundException') {
          errorMessage = 'We could not find an account with this email.';
        } else if (error.name === 'LimitExceededException') {
          errorMessage = 'You have tried resetting too many times. Try again after sometime.';
        } else if (error.name === 'CodeDeliveryFailureException') {
          errorMessage = 'We had trouble sending the reset code. Check your email setup or try again.';
        } else if (error.name === 'InvalidParameterException') {
          errorMessage = 'That email does not look right. Please check the format of email.';
        }
        this.snackbar.color = 'error';
        this.snackbar.message = errorMessage;
        this.snackbar.show = true;
      }
    },

    // sending the code for password
    async handleResetPasswordNextSteps(output) {
      const { nextStep } = output;
      switch (nextStep.resetPasswordStep) {
        case 'CONFIRM_RESET_PASSWORD_WITH_CODE':
          const codeDeliveryDetails = nextStep.codeDeliveryDetails;
          this.snackbar.color = 'success';
          this.snackbar.message = `We have sent a code to ${codeDeliveryDetails.destination}. Check your email!`;
          this.snackbar.show = true;
          this.formMode = 'confirmResetPassword';
          break;
        case 'DONE':
          console.log('Successfully reset password.');
          this.snackbar.color = 'success';
          this.snackbar.message = 'Password reset complete! Log in with your new Password.';
          this.snackbar.show = true;

          setTimeout(() => {
            this.formMode = 'login';
          }, 2000);  
          this.loading = false;
          break;
      }
    },

    // OTP-code with new password
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
        console.error('Confirm reset password error:', error);
        console.log('Error details:', JSON.stringify(error, null, 2));
        let errorMessage = 'Something went wrong while resetting your password. Please try again.';
        if (error.name === 'CodeMismatchException') {
          errorMessage = 'That verification code is not correct. Check it and try again.';
        } else if (error.name === 'ExpiredCodeException') {
          errorMessage = 'The code has expired.';
        } else if (error.name === 'InvalidPasswordException') {
          errorMessage = 'Your new password needs to be stronger (at least 8 characters).';
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
          newPassword: this.password
        });
        this.snackbar.color = 'success';
        this.snackbar.message = 'Password changed Successfully';
        this.snackbar.show = true;
        this.formMode = 'login';
      } catch (err) {
        console.error('Password update error:', err);
        let errorMessage = 'Something went wrong while changing your password.'

        if (err.name === 'NotAuthorizedException') {
          errorMessage = 'Old password is incorrect.';
        } else if (err.name === 'InvalidPasswordException') {
          errorMessage = 'New Password does not meet requirements.';
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
      this.signupUsername = '';
      this.email = '';
      this.resetEmail = '';
      this.password = '';
      this.oldPassword = '';
      this.verificationCode = '';
      this.showVerification = false;
      this.resetCooldown = false;
      this.snackbar.show = false;
    },
  },

  watch: {
    formMode(newMode, oldMode) {
      if (newMode !== oldMode) {
        this.resetForm(newMode);
      }
    },
  },
}      
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
</style>