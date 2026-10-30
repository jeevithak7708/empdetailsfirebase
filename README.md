
# Ex.No:1 To create a employee details fields and to display the employee details using Firebase Database in Android Studio.


## AIM:

To create and display the employee details using Firebase Database in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as HelloWorld and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Display the employee details in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to print the DatabaseTable using the firebasedatabase”.


Developed by: JEEVITHA K
Registeration Number : 212225040149



java program:



import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.LinearLayout;
import android.widget.TextView;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;

import com.google.firebase.database.DataSnapshot;
import com.google.firebase.database.DatabaseError;
import com.google.firebase.database.DatabaseReference;
import com.google.firebase.database.FirebaseDatabase;
import com.google.firebase.database.ValueEventListener;

public class MainActivity extends AppCompatActivity {

    EditText etName, etAge, etSalary;
    Button btnAdd, btnUpdate, btnDelete;
    LinearLayout employeeList;

    DatabaseReference database;
    String selectedId = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        etName = findViewById(R.id.etName);
        etAge = findViewById(R.id.etAge);
        etSalary = findViewById(R.id.etSalary);

        btnAdd = findViewById(R.id.btnAdd);
        btnUpdate = findViewById(R.id.btnUpdate);
        btnDelete = findViewById(R.id.btnDelete);
        employeeList = findViewById(R.id.employeeList);

        database = FirebaseDatabase.getInstance()
                .getReference("employees");

        btnAdd.setOnClickListener(v -> addEmployee());
        btnUpdate.setOnClickListener(v -> updateEmployee());
        btnDelete.setOnClickListener(v -> deleteEmployee());

        displayEmployees();
    }

    private Employee getEmployee() {
        String name = etName.getText().toString().trim();
        String ageText = etAge.getText().toString().trim();
        String salaryText = etSalary.getText().toString().trim();

        if (name.isEmpty() || ageText.isEmpty()
                || salaryText.isEmpty()) {
            Toast.makeText(this, "Enter all details",
                    Toast.LENGTH_SHORT).show();
            return null;
        }

        try {
            int age = Integer.parseInt(ageText);
            double salary = Double.parseDouble(salaryText);

            if (age <= 0 || salary < 0
                    || Double.isNaN(salary)
                    || Double.isInfinite(salary)) {
                Toast.makeText(this, "Enter valid details",
                        Toast.LENGTH_SHORT).show();
                return null;
            }

            return new Employee(name, age, salary);

        } catch (NumberFormatException e) {
            Toast.makeText(this, "Invalid age or salary",
                    Toast.LENGTH_SHORT).show();
            return null;
        }
    }

    private void addEmployee() {
        Employee employee = getEmployee();

        if (employee == null) return;

        String id = database.push().getKey();

        if (id == null) {
            Toast.makeText(this, "Unable to create ID",
                    Toast.LENGTH_SHORT).show();
            return;
        }

        database.child(id).setValue(employee)
                .addOnSuccessListener(unused -> {
                    Toast.makeText(this, "Employee added",
                            Toast.LENGTH_SHORT).show();
                    clearFields();
                })
                .addOnFailureListener(e ->
                        Toast.makeText(this, e.getMessage(),
                                Toast.LENGTH_SHORT).show());
    }

    private void displayEmployees() {
        database.addValueEventListener(new ValueEventListener() {

            @Override
            public void onDataChange(
                    @NonNull DataSnapshot snapshot) {

                employeeList.removeAllViews();

                for (DataSnapshot data : snapshot.getChildren()) {
                    Employee employee =
                            data.getValue(Employee.class);

                    if (employee == null) continue;

                    String id = data.getKey();

                    TextView item = new TextView(
                            MainActivity.this);

                    item.setText("Name: " + employee.name
                            + "\nAge: " + employee.age
                            + "\nSalary: " + employee.salary
                            + "\nTap to select");

                    item.setTextSize(18);
                    item.setPadding(15, 20, 15, 20);

                    employeeList.addView(item);

                    item.setOnClickListener(v -> {
                        selectedId = id;
                        etName.setText(employee.name);
                        etAge.setText(String.valueOf(employee.age));
                        etSalary.setText(String.valueOf(employee.salary));
                    });
                }
            }

            @Override
            public void onCancelled(
                    @NonNull DatabaseError error) {
                Toast.makeText(MainActivity.this,
                        error.getMessage(), Toast.LENGTH_SHORT).show();
            }
        });
    }

    private void updateEmployee() {
        if (selectedId == null) {
            Toast.makeText(this, "Select an employee",
                    Toast.LENGTH_SHORT).show();
            return;
        }

        Employee employee = getEmployee();
        if (employee == null) return;

        database.child(selectedId).setValue(employee)
                .addOnSuccessListener(unused -> {
                    Toast.makeText(this, "Employee updated",
                            Toast.LENGTH_SHORT).show();
                    clearFields();
                })
                .addOnFailureListener(e ->
                        Toast.makeText(this, e.getMessage(),
                                Toast.LENGTH_SHORT).show());
    }

    private void deleteEmployee() {
        if (selectedId == null) {
            Toast.makeText(this, "Select an employee",
                    Toast.LENGTH_SHORT).show();
            return;
        }

        database.child(selectedId).removeValue()
                .addOnSuccessListener(unused -> {
                    Toast.makeText(this, "Employee deleted",
                            Toast.LENGTH_SHORT).show();
                    clearFields();
                })
                .addOnFailureListener(e ->
                        Toast.makeText(this, e.getMessage(),
                                Toast.LENGTH_SHORT).show());
    }

    private void clearFields() {
        etName.setText("");
        etAge.setText("");
        etSalary.setText("");
        selectedId = null;
    }
}

XML program:

<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="20dp">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Employee Management"
            android:textSize="24sp"
            android:textStyle="bold"
            android:gravity="center"
            android:padding="10dp" />

        <EditText
            android:id="@+id/etName"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Employee Name"
            android:inputType="textPersonName" />

        <EditText
            android:id="@+id/etAge"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Employee Age"
            android:inputType="number" />

        <EditText
            android:id="@+id/etSalary"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Employee Salary"
            android:inputType="numberDecimal" />

        <Button
            android:id="@+id/btnAdd"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Add Employee" />

        <Button
            android:id="@+id/btnUpdate"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Update Employee" />

        <Button
            android:id="@+id/btnDelete"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Delete Employee" />

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Employee List"
            android:textSize="20sp"
            android:textStyle="bold"
            android:padding="10dp" />

        <LinearLayout
            android:id="@+id/employeeList"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical" />

    </LinearLayout>

</ScrollView>

*/
```

## OUTPUT

<img width="1912" height="1031" alt="image" src="https://github.com/user-attachments/assets/4982ca5d-5b75-4996-b7b5-0b7327821b95" />

<img width="392" height="825" alt="image" src="https://github.com/user-attachments/assets/fe880962-086f-4f73-8b14-981049e937f4" />





## RESULT
Thus a Simple Android Application create a firebase database and to display the employee details using Firbase Real Time Database in Android Studio is developed and executed successfully.
