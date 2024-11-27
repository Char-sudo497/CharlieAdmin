<template>
    <v-container>
        <!-- First Table (Customer Management) -->
        <v-row>
            <v-col cols="12" class="mb-4">
                <v-card class="elevation-2">
                    <v-card-title>
                        <span class="headline">Customer Management</span>
                        <v-spacer></v-spacer>
                        <v-btn color="primary" icon @click="openAddDialog('customer')">
                            <v-icon>mdi-plus</v-icon>
                        </v-btn>
                    </v-card-title>
                    <v-data-table :headers="userHeaders" :items="users" item-key="uid" class="elevation-1">
                        <template v-slot:item.actions="{ item }">
                            <v-btn color="blue" icon @click="editUser(item)">
                                <v-icon>mdi-pencil</v-icon>
                            </v-btn>
                            <v-btn color="red" icon @click="deleteUser(item.uid)">
                                <v-icon>mdi-delete</v-icon>
                            </v-btn>
                        </template>
                    </v-data-table>
                </v-card>
            </v-col>
        </v-row>

        <v-dialog v-model="editDialogVisible" max-width="600px">
            <v-card>
                <v-card-title>Edit User Details</v-card-title>
                <v-divider></v-divider>
                <v-card-text>
                    <v-form ref="editForm" v-model="isValid" lazy-validation>
                        <v-text-field v-model="userForm.firstName" label="First Name" outlined dense
                            required></v-text-field>
                        <v-text-field v-model="userForm.lastName" label="Last Name" outlined dense
                            required></v-text-field>
                        <v-text-field v-model="userForm.email" label="Email" outlined dense required></v-text-field>
                        <v-select v-model="userForm.role" :items="roles" label="Role" outlined dense
                            required></v-select>
                    </v-form>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn text @click="closeEditDialog">Cancel</v-btn>
                    <v-btn text color="primary" @click="submitUserForm">Save</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <!-- Add Dialog -->
        <v-dialog v-model="addDialogVisible" max-width="600px">
            <v-card>
                <v-card-title>Add New {{ userForm.role }}</v-card-title>
                <v-divider></v-divider>
                <v-card-text>
                    <v-form ref="addForm" v-model="isValid" lazy-validation>
                        <v-text-field v-model="userForm.firstName" label="First Name" outlined dense
                            required></v-text-field>
                        <v-text-field v-model="userForm.lastName" label="Last Name" outlined dense
                            required></v-text-field>
                        <v-text-field v-model="userForm.email" label="Email" outlined dense required></v-text-field>
                        <v-text-field v-model="userForm.password" label="Password" type="password" outlined dense
                            required></v-text-field>
                        <v-select v-model="userForm.role" :items="roles" label="Role" outlined dense required
                            :disabled="roleFixed"></v-select>
                    </v-form>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn text @click="closeAddDialog">Cancel</v-btn>
                    <v-btn text color="primary" @click="submitUserForm">Save</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <!-- Second Table (Business Owner Management) -->
        <v-row>
            <v-col cols="12" class="mb-4">
                <v-card class="elevation-2">
                    <v-card-title class="headline text-center">
                        <v-icon class="mr-2">mdi-account-multiple</v-icon>
                        Business Owner Management
                        <v-spacer></v-spacer>
                        <v-btn color="primary" icon @click="openAddDialog('customer')">
                            <v-icon>mdi-plus</v-icon>
                        </v-btn>
                    </v-card-title>
                    <v-data-table :headers="userHeaders" :items="businessOwners" item-key="uid" class="elevation-1"
                        :header-class="{ 'text-align-center': true }">
                        <template v-slot:item.actions="{ item }">
                            <v-btn color="blue" icon @click="editUser(item)">
                                <v-icon>mdi-pencil</v-icon>
                            </v-btn>
                            <v-btn color="red" icon @click="deleteUser(item.uid)">
                                <v-icon>mdi-delete</v-icon>
                            </v-btn>
                        </template>
                    </v-data-table>
                </v-card>
            </v-col>
        </v-row>

        <!-- Third Table (Cashier Management) -->
        <v-row>
            <v-col cols="12" class="mb-4">
                <v-card class="elevation-2">
                    <v-card-title class="headline text-center">
                        <v-icon class="mr-2">mdi-account-multiple</v-icon>
                        Cashier Management
                        <v-spacer></v-spacer>
                        <v-btn color="primary" icon @click="openAddDialog('customer')">
                            <v-icon>mdi-plus</v-icon>
                        </v-btn>
                    </v-card-title>
                    <v-data-table :headers="userHeaders" :items="cashiers" item-key="uid" class="elevation-1"
                        :header-class="{ 'text-align-center': true }">
                        <template v-slot:item.actions="{ item }">
                            <v-btn color="blue" icon @click="editUser(item)">
                                <v-icon>mdi-pencil</v-icon>
                            </v-btn>
                            <v-btn color="red" icon @click="deleteUser(item.uid)">
                                <v-icon>mdi-delete</v-icon>
                            </v-btn>
                        </template>
                    </v-data-table>
                </v-card>
            </v-col>
        </v-row>

        <!-- Fourth Table (Admin Management) -->
        <v-row>
            <v-col cols="12" class="mb-4">
                <v-card class="elevation-2">
                    <v-card-title class="headline text-center">
                        <v-icon class="mr-2">mdi-account-multiple</v-icon>
                        Admin Management
                        <v-spacer></v-spacer>
                        <v-btn color="primary" icon @click="openAddDialog('customer')">
                            <v-icon>mdi-plus</v-icon>
                        </v-btn>
                    </v-card-title>
                    <v-data-table :headers="userHeaders" :items="admins" item-key="uid" class="elevation-1"
                        :header-class="{ 'text-align-center': true }">
                        <template v-slot:item.actions="{ item }">
                            <v-btn color="blue" icon @click="editUser(item)">
                                <v-icon>mdi-pencil</v-icon>
                            </v-btn>
                            <v-btn color="red" icon @click="deleteUser(item.uid)">
                                <v-icon>mdi-delete</v-icon>
                            </v-btn>
                        </template>
                    </v-data-table>
                </v-card>
            </v-col>
        </v-row>
    </v-container>
</template>

<script>
import { collection, query, where, getDocs, addDoc, updateDoc, doc, deleteDoc } from 'firebase/firestore';
import { firestore } from '~/plugins/firebase';

export default {
    data() {
        return {
            userHeaders: [
                { text: 'First Name', value: 'firstName' },
                { text: 'Last Name', value: 'lastName' },
                { text: 'Email', value: 'email' },
                { text: 'Role', value: 'role' },
                { text: 'Actions', value: 'actions', sortable: false }
            ],
            addDialogVisible: false, // Add dialog visibility
            roleFixed: false, // Locks role in add dialog
            editDialogVisible: false, // Controls the visibility of the edit dialog
            isValid: false,           // Tracks form validation state
            users: [],
            businessOwners: [],
            cashiers: [],
            admins: [],
            roles: ['admin', 'business owner', 'customer', 'cashier'],
            deleteDialog: false,
            editingUserID: null,
            userForm: {
                firstName: '',
                lastName: '',
                email: '',
                password: '',
                role: 'customer' // default to customer
            }
        };
    },
    created() {
        this.fetchUsers();
        this.fetchBusinessOwners();
        this.fetchCashiers();
        this.fetchAdmins();
    },
    methods: {
        // Fetch users from Firestore
        async fetchUsers() {
            try {
                const q = query(collection(firestore, 'Users'), where('role', '==', 'customer'));
                const usersSnapshot = await getDocs(q);
                this.users = usersSnapshot.docs.map(doc => ({
                    uid: doc.id,
                    firstName: doc.data().firstName,
                    lastName: doc.data().lastName,
                    email: doc.data().email,
                    role: doc.data().role
                }));
            } catch (error) {
                console.error("Error fetching users: ", error);
            }
        },

        // Add new user or update existing user
        async submitUserForm() {
            if (this.editingUserID) {
                await this.updateUser(); // Update user if editing
            } else {
                await this.addUser(); // Add new user if no editingUserID
            }
            this.editDialogVisible = false; // Close dialog after saving
        },

        /// Add new user
        async addUser() {
            try {
                // Create the new user document with createdAt timestamp, userID (uid), and other fields
                const userRef = await addDoc(collection(firestore, 'Users'), {
                    firstName: this.userForm.firstName,
                    lastName: this.userForm.lastName,
                    email: this.userForm.email,
                    password: this.userForm.password,
                    role: this.userForm.role,
                    createdAt: new Date(), // Timestamp when the user is added
                });

                // Update the document to include the userID field (equal to the document's ID)
                await updateDoc(userRef, {
                    userID: userRef.id
                });

                this.resetForm();
                this.fetchUsers(); // Re-fetch users to update the list
            } catch (error) {
                console.error("Error adding user: ", error);
            }
        },

        // Edit user details
        editUser(user) {
            this.editingUserID = user.uid;
            this.userForm = { ...user }; // Populate the form with selected user's data
            this.editDialogVisible = true; // Open the dialog
        },
        openAddDialog(role) {
            this.resetForm();
            this.userForm.role = role;
            this.roleFixed = true; // Lock the role dropdown
            this.addDialogVisible = true;
        },

        // Update user details
        // Update user details
        async updateUser() {
            try {
                const userRef = doc(firestore, 'Users', this.editingUserID);
                await updateDoc(userRef, {
                    firstName: this.userForm.firstName,
                    lastName: this.userForm.lastName,
                    email: this.userForm.email,
                    password: this.userForm.password,
                    role: this.userForm.role,
                    userID: this.editingUserID // Ensure userID is saved in the document
                });
                this.resetForm();
                this.fetchUsers(); // Re-fetch users to update the list
            } catch (error) {
                console.error("Error updating user: ", error);
            }
        },

        // Delete user
        async deleteUser(userID) {
            try {
                const userRef = doc(firestore, 'Users', userID);
                await deleteDoc(userRef);
                this.fetchUsers(); // Re-fetch users to update the list
            } catch (error) {
                console.error("Error deleting user: ", error);
            }
        },

        // Reset the form fields
        resetForm() {
            this.userForm = {
                firstName: '',
                lastName: '',
                email: '',
                password: '',
                role: 'customer'
            };
            this.editingUserID = null;
        },

        // Fetch business owners, cashiers, and admins in similar fashion as users
        async fetchBusinessOwners() {
            try {
                const q = query(collection(firestore, 'Users'), where('role', '==', 'business owner'));
                const usersSnapshot = await getDocs(q);
                this.businessOwners = usersSnapshot.docs.map(doc => ({
                    uid: doc.id,
                    firstName: doc.data().firstName,
                    lastName: doc.data().lastName,
                    email: doc.data().email,
                    role: doc.data().role
                }));
            } catch (error) {
                console.error("Error fetching business owners: ", error);
            }
        },
        async fetchCashiers() {
            try {
                const q = query(collection(firestore, 'Users'), where('role', '==', 'cashier'));
                const cashiersSnapshot = await getDocs(q);
                this.cashiers = cashiersSnapshot.docs.map(doc => ({
                    uid: doc.id,
                    firstName: doc.data().firstName,
                    lastName: doc.data().lastName,
                    email: doc.data().email,
                    role: doc.data().role
                }));
            } catch (error) {
                console.error("Error fetching cashiers: ", error);
            }
        },
        async fetchAdmins() {
            try {
                const q = query(collection(firestore, 'Users'), where('role', '==', 'admin'));
                const adminsSnapshot = await getDocs(q);
                this.admins = adminsSnapshot.docs.map(doc => ({
                    uid: doc.id,
                    firstName: doc.data().firstName,
                    lastName: doc.data().lastName,
                    email: doc.data().email,
                    role: doc.data().role
                }));
            } catch (error) {
                console.error("Error fetching admins: ", error);
            }
        },
        closeEditDialog() {
            this.editDialogVisible = false;
            this.resetForm();
        },
        // Close Add Dialog
        closeAddDialog() {
            this.addDialogVisible = false;
            this.resetForm();
        },
    }
};
</script>

<style>
.dashboard-card {
    background-color: #333;
    color: white;
    padding: 40px;
}
</style>