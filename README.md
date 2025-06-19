Student Course Enrollment System (with Knapsack Optimization)

A terminal-based Python application with an SQLite backend that allows students to enroll in the most efficient combination of courses using a simplified knapsack algorithm. The system supports role-based access for students, teachers, principals, and admins.

---

Features

Student
- View available courses
- Optionally select one course manually
- Automatically fill remaining slots with a credit-optimized selection
- Prevents duplicate enrollments

Teacher (planned)
- Assign and manage grades
- View student enrollments

Principal/Admin (planned)
- View all student records
- Clear all grades and enrollments

---

How It Works

- Maximum: 4 courses
- Maximum: 7 total credits
- Student may manually select one course
- Remaining courses are chosen using a simplified knapsack approach via itertools.combinations

---

Technologies Used

- Python 3
- SQLite3 – for lightweight local database
- itertools – to generate course combinations

---

How to Run

1. Clone the repository:
   git clone https://github.com/your-username/student-course-knapsack.git
   cd student-course-knapsack

2. Run the application:
   python your_script.py

3. Login credentials:
   - Student: student / 1234
   - Teacher: teacher / abcd
   - Principal: principal / admin1
   - Admin: admin / root

---

License

This project is licensed under the MIT License. See the LICENSE file for details.
