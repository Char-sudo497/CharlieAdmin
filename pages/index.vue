<template>
  <v-container class="fill-height d-flex align-center justify-center" fluid>
    <v-row align="center" justify="center">
      <v-col cols="12" sm="8" md="5">
        <v-card class="sign-in-card">
          <v-card-text class="text-center mt-4">
            <h2>Sign In as Admin</h2>
          </v-card-text>

          <v-card-text>
            <v-form ref="form" v-model="valid" lazy-validation>
              <v-text-field v-model="email" :rules="emailRules" label="Email" required class="input-field mt-2">
                <template v-slot:prepend>
                  <v-icon>mdi-email</v-icon>
                </template>
              </v-text-field>

              <v-text-field v-model="password" :rules="passwordRules" label="Password"
                :type="showPassword ? 'text' : 'password'" required class="input-field mt-2">
                <template v-slot:prepend>
                  <v-icon>mdi-lock</v-icon>
                </template>
                <template v-slot:append>
                  <v-btn icon @click="showPassword = !showPassword">
                    <v-icon>{{ showPassword ? 'mdi-eye-off' : 'mdi-eye' }}</v-icon>
                  </v-btn>
                </template>
              </v-text-field>

              <v-btn @click="signIn" :disabled="loading" class="primary-btn full-width" block
                style="background-color: #000; color: #fff; font-weight: bold;">
                <v-icon v-if="loading" left>mdi-loading</v-icon>
                <span v-if="!loading">SIGN IN</span>
                <span v-else>Signing In...</span>
              </v-btn>
            </v-form>
          </v-card-text>

          <div class="text-center mt-1">
            <v-btn text @click="forgotPasswordDialog = true" class="text-link" style="padding: 0; margin: 0;">
              Forgot Password?
            </v-btn>
          </div>
        </v-card>
      </v-col>
    </v-row>

    <!-- Forgot Password Dialog -->
    <v-dialog v-model="forgotPasswordDialog" max-width="400px">
      <v-card>
        <v-card-title class="headline">Reset Password</v-card-title>
        <v-card-text>
          <p>Please enter your email address to reset your password:</p>
          <v-text-field v-model="resetEmail" :rules="emailRules" label="Email" required>
            <template v-slot:prepend>
              <v-icon>mdi-email</v-icon>
            </template>
          </v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn style="background-color: #ffa900; color: white" @click="sendResetEmail">Send</v-btn>
          <v-btn text @click="forgotPasswordDialog = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import { auth, firestore } from '~/plugins/firebase';
import { signInWithEmailAndPassword, sendPasswordResetEmail } from 'firebase/auth';
import { doc, getDoc } from 'firebase/firestore';

export default {
  data() {
    return {
      middleware: 'admin',
      loading: false,
      valid: false,
      email: '',
      password: '',
      showPassword: false,
      forgotPasswordDialog: false,
      resetEmail: '',
      emailRules: [v => !!v || 'Email is required', v => /.+@.+\..+/.test(v) || 'E-mail must be valid'],
      passwordRules: [v => !!v || 'Password is required', v => v.length >= 6 || 'Password must be at least 6 characters'],
    };
  },
  methods: {
    async signIn() {
      if (this.$refs.form.validate()) {
        this.loading = true;
        try {
          const userCredential = await signInWithEmailAndPassword(auth, this.email, this.password);
          const userId = userCredential.user.uid;

          // Fetch user role from Firestore
          const userDocRef = doc(firestore, 'Users', userId);
          const userDoc = await getDoc(userDocRef);

          if (userDoc.exists()) {
            const userRole = userDoc.data().role;

            // Log the user role to the console
            console.log(`User role is: ${userRole}`);

            if (['admin', 'cashier', 'owner'].includes(userRole)) {
              this.$router.push('/admin/admindashboard');
            } else {
              this.$router.push('/no-access');
            }
          } else {
            console.error("User role not found in Firestore");
          }
        } catch (error) {
          console.error("Error signing in:", error);
          alert("Sign-in failed: " + error.message);
        } finally {
          this.loading = false;
        }
      }
    },
    async sendResetEmail() {
      if (this.resetEmail) {
        try {
          await sendPasswordResetEmail(auth, this.resetEmail);
          this.forgotPasswordDialog = false;
          alert("Password reset email sent!");
        } catch (error) {
          console.error("Error sending password reset email:", error);
          alert("Error sending password reset email: " + error.message);
        }
      } else {
        alert("Please enter your email to reset your password.");
      }
    },
    goToSignUp() {
      this.$router.push('/sign/signup');
    },
  },
};
</script>

<style scoped>
/* Add your styles here */
.fill-height {
  height: 100vh;
  background-color: white;
}

.sign-in-card {
  width: 80%;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.primary-btn {
  font-weight: bold;
  border-radius: 24px;
  height: 48px;
}

.outlined-btn1 {
  font-weight: bold;
  border-radius: 24px;
  height: 48px;
  margin-bottom: 10px;
  margin-top: -10px;
}

.divider {
  color: gray;
  font-size: 14px;
}

.text-link {
  font-size: 14px;
  color: #007bff;
  text-decoration: underline;
  cursor: pointer;
}

.additional-info {
  font-size: 12px;
  color: gray;
}

.input-field {
  margin-top: 10px;
}

.text-center {
  margin-top: 2px;
}

.text-center h2 {
  font-size: 32px;
  color: black;
}
</style>