<template>
    <v-container>
        <!-- First Table (Customer Management) -->
        <v-row>
            <v-col cols="12" class="mb-4">
                <v-card class="elevation-2">
                    <v-card-title>
                        <span class="headline">Customer Management</span>
                        <v-spacer></v-spacer>
                        <v-text-field v-model="customerSearchQuery" label="Search Customers by Name" outlined dense
                            class="mr-3" />
                        <v-btn color="primary" icon @click="openAddDialog('customer')">
                            <v-icon>mdi-plus</v-icon>
                        </v-btn>
                    </v-card-title>

                    <v-data-table :headers="userHeaders" :items="filteredCustomers" item-key="uid" class="elevation-1">
                        <template v-slot:item.fullName="{ item }">
                            {{ item.fullName }}
                        </template>
                        <template v-slot:item.phone="{ item }">
                            {{ item.phone || 'N/A' }}
                        </template>
                        <template v-slot:item.actions="{ item }">
                            <v-btn color="blue" icon @click="editUser(item)">
                                <v-icon>mdi-pencil</v-icon>
                            </v-btn>
                            <v-btn color="red" icon @click="confirmDelete(item.uid)">
                                <v-icon>mdi-delete</v-icon>
                            </v-btn>
                        </template>
                    </v-data-table>
                </v-card>
            </v-col>
        </v-row>

        <!-- Delete Confirmation Dialog -->
        <v-dialog v-model="deleteDialog" max-width="400px">
            <v-card>
                <v-card-title class="headline">Confirm Deletion</v-card-title>
                <v-card-text>Are you sure you want to delete this user?</v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn text @click="deleteDialog = false">Cancel</v-btn>
                    <v-btn text color="red" @click="deleteConfirmed">Delete</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

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
                        <v-text-field v-model="userForm.phone" label="Phone" outlined dense required></v-text-field>
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
                        <v-text-field v-model="userForm.phone" label="Phone" outlined dense required></v-text-field>
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
                        <v-text-field v-model="businessOwnerSearchQuery" label="Search Business Owners by Name" outlined
                            dense class="mr-3" />
                        <v-btn color="primary" icon @click="openAddDialog('Business Owner')">
                            <v-icon>mdi-plus</v-icon>
                        </v-btn>
                    </v-card-title>
                    <v-data-table :headers="userHeaders" :items="filteredBusinessOwners" item-key="uid"
                        class="elevation-1" :header-class="{ 'text-align-center': true }">
                        <template v-slot:item.fullName="{ item }">
                            {{ item.fullName }}
                        </template>
                        <template v-slot:item.phone="{ item }">
                            {{ item.phone || 'N/A' }}
                        </template>
                        <template v-slot:item.actions="{ item }">
                            <v-btn color="blue" icon @click="editUser(item)">
                                <v-icon>mdi-pencil</v-icon>
                            </v-btn>
                            <v-btn color="red" icon @click="confirmDelete(item.uid)">
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
                        <v-text-field v-model="cashierSearchQuery" label="Search Cashiers by Name" outlined dense
                            class="mr-3" />
                        <v-btn color="primary" icon @click="openAddDialog('cashier')">
                            <v-icon>mdi-plus</v-icon>
                        </v-btn>
                    </v-card-title>
                    <v-data-table :headers="userHeaders" :items="filteredCashiers" class="elevation-1"
                        :header-class="{ 'text-align-center': true }">
                        <template v-slot:item.fullName="{ item }">
                            {{ item.fullName }}
                        </template>
                        <template v-slot:item.phone="{ item }">
                            {{ item.phone || 'N/A' }}
                        </template>
                        <template v-slot:item.actions="{ item }">
                            <v-btn color="blue" icon @click="editUser(item)">
                                <v-icon>mdi-pencil</v-icon>
                            </v-btn>
                            <v-btn color="red" icon @click="confirmDelete(item.uid)">
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
                        <v-text-field v-model="searchQuery" label="Search Admins by Name" outlined dense
                            class="mr-3"></v-text-field>
                        <v-btn color="primary" icon @click="openAddDialog('admin')">
                            <v-icon>mdi-plus</v-icon>
                        </v-btn>
                    </v-card-title>
                    <v-data-table :headers="userHeaders" :items="filteredAdmins" item-key="uid" class="elevation-1"
                        :header-class="{ 'text-align-center': true }">
                        <template v-slot:item.fullName="{ item }">
                            {{ item.fullName }}
                        </template>
                        <template v-slot:item.phone="{ item }">
                            {{ item.phone || 'N/A' }}
                        </template>
                        <template v-slot:item.actions="{ item }">
                            <v-btn color="blue" icon @click="editUser(item)">
                                <v-icon>mdi-pencil</v-icon>
                            </v-btn>
                            <v-btn color="red" icon @click="confirmDelete(item.uid)">
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
import { collection, query, where, onSnapshot, addDoc, updateDoc, doc, deleteDoc } from 'firebase/firestore';
import { firestore } from '~/plugins/firebase';

export default {
    data() {
        return {
            searchQuery: '', // Search input value
            cashierSearchQuery: '', // Search input for Cashier Management
            businessOwnerSearchQuery: '',
            customerSearchQuery: '', // Search query for customers
            userHeaders: [
                { text: 'First Name', value: 'firstName' },
                { text: 'Last Name', value: 'lastName' },
                { text: 'Full Name', value: 'fullName' }, // New Full Name column
                { text: 'Email', value: 'email' },
                { text: 'Phone', value: 'phone' }, // Add Phone column
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
            roles: ['admin', 'Business Owner', 'customer', 'cashier'],
            deleteDialog: false,
            editingUserID: null,
            deleteDialog: false,
            selectedUser: null,
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
        this.subscribeToUsers();
        this.subscribeToBusinessOwners();
        this.subscribeToCashiers();
        this.subscribeToAdmins();
    },
    computed: {
        filteredCustomers() {
            if (!this.customerSearchQuery) return this.users; // If no search query, return all users
            const query = this.customerSearchQuery.toLowerCase();
            return this.users.filter(user =>
                user.fullName.toLowerCase().includes(query)
            );
        },
        // Filter Business Owners by Full Name
        filteredBusinessOwners() {
            if (!this.businessOwnerSearchQuery) return this.businessOwners; // Return all if no search query
            const query = this.businessOwnerSearchQuery.toLowerCase();
            return this.businessOwners.filter(owner =>
                owner.fullName.toLowerCase().includes(query)
            );
        },
        filteredCashiers() {
            if (!this.cashierSearchQuery) return this.cashiers; // Return all cashiers if no query
            const query = this.cashierSearchQuery.toLowerCase();
            return this.cashiers.filter(cashier =>
                cashier.fullName.toLowerCase().includes(query)
            );
        },
        filteredAdmins() {
            if (!this.searchQuery) return this.admins; // If searchQuery is empty, show all admins
            return this.admins.filter(admin =>
                admin.fullName.toLowerCase().includes(this.searchQuery.toLowerCase())
            );
        },
    },
    methods: {
        fetchUsers() {
            const q = query(collection(firestore, 'Users'), where('role', '==', 'customer'));
            onSnapshot(q, (snapshot) => {
                this.users = snapshot.docs.map(doc => ({
                    uid: doc.id,
                    firstName: doc.data().firstName,
                    lastName: doc.data().lastName,
                    fullName: `${doc.data().firstName} ${doc.data().lastName}`,
                    email: doc.data().email,
                    phone: doc.data().phone,
                    role: doc.data().role,
                }));
            });
        },
        // Show delete confirmation dialog
        confirmDelete(userID) {
            this.selectedUser = userID; // Store user ID to delete
            this.deleteDialog = true;  // Open confirmation dialog
        },
        // Perform deletion after confirmation
        async deleteConfirmed() {
            try {
                if (this.selectedUser) {
                    const userRef = doc(firestore, 'Users', this.selectedUser);
                    await deleteDoc(userRef);
                    this.fetchUsers(); // Ensure fetchUsers exists or remove this line.
                }
            } catch (error) {
                console.error("Error deleting user:", error);
            } finally {
                this.deleteDialog = false;
                this.selectedUser = null;
            }
        },
        /// Subscribe to real-time updates for customers
        subscribeToUsers() {
            const q = query(collection(firestore, 'Users'), where('role', '==', 'customer'));
            onSnapshot(q, (snapshot) => {
                this.users = snapshot.docs.map(doc => ({
                    uid: doc.id,
                    firstName: doc.data().firstName,
                    lastName: doc.data().lastName,
                    fullName: `${doc.data().firstName} ${doc.data().lastName}`, // Combine first and last names
                    email: doc.data().email,
                    phone: doc.data().phone,
                    role: doc.data().role
                }));
            });
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
                    phone: this.userForm.phone, // Include phone field
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

                // Build the update payload, omitting empty fields
                const updatePayload = {
                    firstName: this.userForm.firstName,
                    lastName: this.userForm.lastName,
                    email: this.userForm.email,
                    role: this.userForm.role,
                };

                // Only include the phone field if it's not empty
                if (this.userForm.phone || this.userForm.phone === '') {
                    updatePayload.phone = this.userForm.phone || ''; // Default to an empty string if phone is empty
                }

                await updateDoc(userRef, updatePayload);

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
                phone: '',
                password: '',
                role: 'customer'
            };
            this.editingUserID = null;
        },

        // Fetch business owners, cashiers, and admins in similar fashion as users
        // Subscribe to real-time updates for business owners
        subscribeToBusinessOwners() {
            const q = query(collection(firestore, 'Users'), where('role', '==', 'Business Owner'));
            onSnapshot(q, (snapshot) => {
                this.businessOwners = snapshot.docs.map(doc => ({
                    uid: doc.id,
                    firstName: doc.data().firstName,
                    lastName: doc.data().lastName,
                    fullName: `${doc.data().firstName} ${doc.data().lastName}`, // Combine first and last names
                    email: doc.data().email,
                    phone: doc.data().phone, // Include phone field
                    role: doc.data().role
                }));
            });
        },
        // Subscribe to real-time updates for cashiers
        subscribeToCashiers() {
            const q = query(collection(firestore, 'Users'), where('role', '==', 'cashier'));
            onSnapshot(q, (snapshot) => {
                this.cashiers = snapshot.docs.map(doc => ({
                    uid: doc.id,
                    firstName: doc.data().firstName,
                    lastName: doc.data().lastName,
                    fullName: `${doc.data().firstName} ${doc.data().lastName}`, // Combine first and last names
                    email: doc.data().email,
                    phone: doc.data().phone, // Include phone field
                    role: doc.data().role
                }));
            });
        },
        subscribeToAdmins() {
            const q = query(collection(firestore, 'Users'), where('role', '==', 'admin'));
            onSnapshot(q, (snapshot) => {
                this.admins = snapshot.docs.map(doc => ({
                    uid: doc.id,
                    firstName: doc.data().firstName,
                    lastName: doc.data().lastName,
                    fullName: `${doc.data().firstName} ${doc.data().lastName}`, // Combine first and last names
                    email: doc.data().email,
                    phone: doc.data().phone, // Include phone field
                    role: doc.data().role
                }));
            });
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