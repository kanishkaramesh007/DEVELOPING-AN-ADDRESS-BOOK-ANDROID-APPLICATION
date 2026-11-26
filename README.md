# DEVELOPING-AN-ADDRESS-BOOK-ANDROID-APPLICATION
To design and develop an Address Book Android application using Android Studio

# AIM
To design and develop an Address Book Android application using Android Studio that allows users to add, store, view and manage contact details such as name, phone number and email address through a user-friendly graphical interface.

# SOFTWARE REQUIREMENTS
•	Android Studio
•	Java Programming Language
•	XML Layout Editor
•	Android Emulator or Android Mobile
•	Android SDK

# THEORY
Android Studio is an Integrated Development Environment (IDE) used for developing Android applications using Java and XML.
An Address Book application is used to maintain and manage personal contact information. It provides a graphical user interface for entering and displaying contact details.
The application uses the following Android UI components:
•	EditText: To enter the contact name, phone number and email address.
•	Button: To add and save contact details.
•	TextView: To display contact information.
•	LinearLayout: To arrange the user interface components.
•	ListView: To display the list of saved contacts.
Java event-handling methods are used to process user inputs and perform contact management operations.

# PROCEDURE
1.	Open Android Studio.
2.	Select New Project and choose Empty Views Activity.
3.	Enter the project name as AddressBook.
4.	Select Java as the programming language.
5.	Design the user interface using the XML Layout Editor.
6.	Add EditText fields to enter the contact name, phone number and email address.
7.	Add a Button to save the contact details.
8.	Add a ListView to display the saved contacts.
9.	Open MainActivity.java and write the Java program to process contact information.
10.	Use setOnClickListener() to handle the Save Contact button event.
11.	Store the entered contact details in an appropriate data structure.
12.	Display the saved contacts in the ListView.
13.	Execute the application using an Android Emulator or physical Android device.
14.	Enter contact details and click the Save Contact button.
15.	Verify that the contact information is displayed successfully.

# ALGORITHM
1.	Start the application.
2.	Initialize the Android user interface components.
3.	Accept the contact name from the user.
4.	Accept the phone number and email address.
5.	Validate the entered contact details.
6.	Click the Save Contact button.
7.	Store the contact information.
8.	Display the saved contact in the contact list.
9.	Repeat the process to add additional contacts.
10.	Stop
# XML CODE
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    android:background="#F5F7FA">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Address Book"
        android:textSize="28sp"
        android:textStyle="bold"
        android:textColor="#1565C0"
        android:gravity="center"
        android:padding="12dp" />

    <EditText
        android:id="@+id/nameInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter name"
        android:inputType="textPersonName" />

    <EditText
        android:id="@+id/phoneInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter phone number"
        android:inputType="phone" />

    <EditText
        android:id="@+id/emailInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter email"
        android:inputType="textEmailAddress" />

    <EditText
        android:id="@+id/addressInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter address"
        android:inputType="textPostalAddress" />

    <Button
        android:id="@+id/saveButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Save Contact" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Saved Contacts"
        android:textSize="20sp"
        android:textStyle="bold"
        android:paddingTop="16dp"
        android:paddingBottom="8dp" />

    <ListView
        android:id="@+id/contactList"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:dividerHeight="1dp" />

     </LinearLayout>
# DATABASE CODE
package com.example.addressbook;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

public class DatabaseHelper extends SQLiteOpenHelper {

    public DatabaseHelper(Context context) {
        super(context, "AddressBook.db", null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL("CREATE TABLE contacts (" +
                "id INTEGER PRIMARY KEY AUTOINCREMENT, " +
                "name TEXT, phone TEXT, email TEXT, address TEXT)");
    }

    @Override
    public void onUpgrade(SQLiteDatabase db,
                          int oldVersion, int newVersion) {
        db.execSQL("DROP TABLE IF EXISTS contacts");
        onCreate(db);
    }

    public boolean addContact(String name, String phone,
                              String email, String address) {
        SQLiteDatabase db = getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put("name", name);
        values.put("phone", phone);
        values.put("email", email);
        values.put("address", address);

        return db.insert("contacts", null, values) != -1;
    }

    public Cursor getContacts() {
        return getReadableDatabase().rawQuery(
                "SELECT * FROM contacts ORDER BY name", null);
    }

    public boolean updateContact(int id, String name,
                                 String phone, String email,
                                 String address) {
        ContentValues values = new ContentValues();
        values.put("name", name);
        values.put("phone", phone);
        values.put("email", email);
        values.put("address", address);

        return getWritableDatabase().update(
                "contacts", values, "id=?",
                new String[]{String.valueOf(id)}) > 0;
    }

    public boolean deleteContact(int id) {
        return getWritableDatabase().delete(
                "contacts", "id=?",
                new String[]{String.valueOf(id)}) > 0;
    }

    @Override
    public void onConfigure(SQLiteDatabase db) {
        super.onConfigure(db);
        db.setForeignKeyConstraintsEnabled(true);
    }
    }
# JAVA PROGRAM


    package com.example.addressbook;

    import android.app.Activity;
    import android.app.AlertDialog;
    import android.os.Bundle;
    import android.database.Cursor;
    import android.graphics.Color;
    import android.view.ViewGroup;
    import android.widget.ArrayAdapter;
    import android.widget.Button;
    import android.widget.EditText;
    import android.widget.LinearLayout;
    import android.widget.ListView;
    import android.widget.TextView;
    import android.widget.Toast;

    import java.util.ArrayList;

    public class MainActivity extends Activity {

    EditText nameInput, phoneInput, emailInput, addressInput;
    Button saveButton;
    ListView contactList;

    DatabaseHelper db;
    ArrayList<Integer> contactIds;
    ArrayList<String> contacts;
    ArrayAdapter<String> adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        nameInput = findViewById(R.id.nameInput);
        phoneInput = findViewById(R.id.phoneInput);
        emailInput = findViewById(R.id.emailInput);
        addressInput = findViewById(R.id.addressInput);
        saveButton = findViewById(R.id.saveButton);
        contactList = findViewById(R.id.contactList);

        db = new DatabaseHelper(this);

        contacts = new ArrayList<>();
        contactIds = new ArrayList<>();

        adapter = new ArrayAdapter<>(
                this,
                android.R.layout.simple_list_item_1,
                contacts
        );

        contactList.setAdapter(adapter);

        saveButton.setOnClickListener(v -> saveContact());

        contactList.setOnItemClickListener((parent, view,
                                            position, id) -> {
            editContact(position);
        });

        contactList.setOnItemLongClickListener(
                (parent, view, position, id) -> {
                    confirmDelete(position);
                    return true;
                });

        loadContacts();
    }

    private void saveContact() {
        String name = nameInput.getText().toString().trim();
        String phone = phoneInput.getText().toString().trim();
        String email = emailInput.getText().toString().trim();
        String address = addressInput.getText().toString().trim();

        if (name.isEmpty() || phone.isEmpty()) {
            Toast.makeText(this,
                    "Enter name and phone number",
                    Toast.LENGTH_SHORT).show();
            return;
        }

        boolean result = db.addContact(
                name, phone, email, address);

        if (result) {
            Toast.makeText(this, "Contact saved",
                    Toast.LENGTH_SHORT).show();

            clearFields();
            loadContacts();
        } else {
            Toast.makeText(this, "Unable to save contact",
                    Toast.LENGTH_SHORT).show();
        }
    }

    private void loadContacts() {
        contacts.clear();
        contactIds.clear();

        try (Cursor cursor = db.getContacts()) {
            while (cursor.moveToNext()) {
                int id = cursor.getInt(
                        cursor.getColumnIndexOrThrow("id"));

                String name = cursor.getString(
                        cursor.getColumnIndexOrThrow("name"));

                String phone = cursor.getString(
                        cursor.getColumnIndexOrThrow("phone"));

                String email = cursor.getString(
                        cursor.getColumnIndexOrThrow("email"));

                String address = cursor.getString(
                        cursor.getColumnIndexOrThrow("address"));

                contactIds.add(id);

                contacts.add(name + "\nPhone: " + phone
                        + "\nEmail: " + email
                        + "\nAddress: " + address);
            }
        }

        adapter.notifyDataSetChanged();
    }

    private void clearFields() {
        nameInput.setText("");
        phoneInput.setText("");
        emailInput.setText("");
        addressInput.setText("");
        nameInput.requestFocus();
    }

    private void editContact(int position) {
        int id = contactIds.get(position);

        try (Cursor cursor = db.getContacts()) {
            while (cursor.moveToNext()) {
                if (cursor.getInt(
                        cursor.getColumnIndexOrThrow("id")) == id) {

                    String name = cursor.getString(
                            cursor.getColumnIndexOrThrow("name"));
                    String phone = cursor.getString(
                            cursor.getColumnIndexOrThrow("phone"));
                    String email = cursor.getString(
                            cursor.getColumnIndexOrThrow("email"));
                    String address = cursor.getString(
                            cursor.getColumnIndexOrThrow("address"));

                    showEditDialog(id, name, phone, email, address);
                    return;
                }
            }
        }
    }

    private void showEditDialog(int id, String name,
                                String phone, String email,
                                String address) {

        LinearLayout layout = new LinearLayout(this);
        layout.setOrientation(LinearLayout.VERTICAL);
        layout.setPadding(32, 8, 32, 0);

        EditText editName = makeField("Name", name);
        EditText editPhone = makeField("Phone", phone);
        EditText editEmail = makeField("Email", email);
        EditText editAddress = makeField("Address", address);

        layout.addView(editName);
        layout.addView(editPhone);
        layout.addView(editEmail);
        layout.addView(editAddress);

        AlertDialog dialog = new AlertDialog.Builder(this)
                .setTitle("Edit Contact")
                .setView(layout)
                .setPositiveButton("Update", null)
                .setNegativeButton("Cancel", null)
                .create();

        dialog.setOnShowListener(d -> {
            dialog.getButton(AlertDialog.BUTTON_POSITIVE)
                    .setOnClickListener(v -> {

                String newName = editName.getText()
                        .toString().trim();
                String newPhone = editPhone.getText()
                        .toString().trim();

                if (newName.isEmpty() || newPhone.isEmpty()) {
                    Toast.makeText(this,
                            "Name and phone are required",
                            Toast.LENGTH_SHORT).show();
                    return;
                }

                boolean updated = db.updateContact(
                        id,
                        newName,
                        newPhone,
                        editEmail.getText().toString().trim(),
                        editAddress.getText().toString().trim()
                );

                if (updated) {
                    loadContacts();
                    Toast.makeText(this, "Contact updated",
                            Toast.LENGTH_SHORT).show();
                    dialog.dismiss();
                } else {
                    Toast.makeText(this, "Update failed",
                            Toast.LENGTH_SHORT).show();
                }
            });
        });

        dialog.show();
    }

    private EditText makeField(String hint, String value) {
        EditText field = new EditText(this);
        field.setHint(hint);
        field.setText(value);
        field.setLayoutParams(new LinearLayout.LayoutParams(
                ViewGroup.LayoutParams.MATCH_PARENT,
                ViewGroup.LayoutParams.WRAP_CONTENT));
        return field;
    }

    private void confirmDelete(int position) {
        int id = contactIds.get(position);

        new AlertDialog.Builder(this)
                .setTitle("Delete Contact")
                .setMessage("Do you want to delete this contact?")
                .setPositiveButton("Delete", (dialog, which) -> {
                    if (db.deleteContact(id)) {
                        loadContacts();
                        Toast.makeText(this,
                                "Contact deleted",
                                Toast.LENGTH_SHORT).show();
                    } else {
                        Toast.makeText(this,
                                "Delete failed",
                                Toast.LENGTH_SHORT).show();
                    }
                })
                .setNegativeButton("Cancel", null)
                .show();
    }

    @Override
    protected void onDestroy() {
        db.close();
        super.onDestroy();
    }
    }
# EXPECTED OUTPUT
<img width="1600" height="760" alt="image" src="https://github.com/user-attachments/assets/a2c25572-ba72-4309-b57c-6a00aa741bcc" />

ADDRESS BOOK APPLICATION
Input Details:
•	Name: Arun Kumar
•	Phone Number: 9876543210
•	Email Address: arun@gmail.com
After clicking the Save Contact button, the application displays:
Saved Contacts
Contact 1:
•	Name: Arun Kumar
•	Phone: 9876543210
•	Email: arun@gmail.com
Contact 2:
•	Name: Priya
•	Phone: 9876501234
•	Email: priya@gmail.com

# APPLICATION FEATURES
•	User-friendly graphical interface.
•	Add new contact details.
•	Store name, phone number and email address.
•	Display saved contacts in a list.
•	Validate user-entered information.
•	Manage multiple contact entries.

# RESULT
Thus, the Address Book Android application was successfully designed and developed using Android Studio with Java and XML. The application allows users to enter, save and display contact details successfully.

# CONCLUSION
The experiment provides practical knowledge of Android application development, XML-based user interface design, Java programming, event handling and contact management using Android Studio.

