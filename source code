import sqlite3
import itertools

def initialize_db():
    conn = sqlite3.connect("college.db")
    cursor = conn.cursor()

    cursor.executescript('''
    CREATE TABLE IF NOT EXISTS Users (
        Username TEXT PRIMARY KEY,
        Password TEXT NOT NULL,
        Role TEXT NOT NULL
    );

    CREATE TABLE IF NOT EXISTS Courses (
        CourseID INTEGER PRIMARY KEY,
        CourseName TEXT NOT NULL,
        Credits INTEGER NOT NULL
    );

    CREATE TABLE IF NOT EXISTS StudentCourses (
        StudentUSN TEXT NOT NULL,
        CourseID INTEGER NOT NULL,
        PRIMARY KEY (StudentUSN, CourseID),
        FOREIGN KEY (CourseID) REFERENCES Courses(CourseID)
    );

    CREATE TABLE IF NOT EXISTS Grades (
        StudentUSN TEXT NOT NULL,
        CourseID INTEGER NOT NULL,
        Grade TEXT,
        PRIMARY KEY (StudentUSN, CourseID),
        FOREIGN KEY (CourseID) REFERENCES Courses(CourseID)
    );

    INSERT OR IGNORE INTO Users (Username, Password, Role) VALUES 
        ('student', '1234', 'student'),
        ('teacher', 'abcd', 'teacher'),
        ('principal', 'admin1', 'principal'),
        ('admin', 'root', 'admin');

    INSERT OR IGNORE INTO Courses (CourseID, CourseName, Credits) VALUES
        (1, 'Maths', 2),
        (2, 'DBMS', 4),
        (3, 'ADA', 2),
        (4, 'Biology', 1),
        (5, 'Computer Science', 3),
        (6, 'AJAVA', 3),
        (7, 'UHV', 1),
        (8, 'UI/UX', 1);
    ''')

    conn.commit()
    conn.close()

def authenticate_user(username, password):
    conn = sqlite3.connect("college.db")
    cursor = conn.cursor()
    cursor.execute("SELECT Role FROM Users WHERE Username=? AND Password=?", (username, password))
    user = cursor.fetchone()
    conn.close()
    return user[0] if user else None

def clear_records():
    conn = sqlite3.connect("college.db")
    cursor = conn.cursor()
    cursor.executescript('''
    DELETE FROM StudentCourses;
    DELETE FROM Grades;
    ''')
    conn.commit()
    conn.close()
    print("All student enrollments and grades have been cleared.")

def knapsack_max_credits(student_usn, max_credits=7, max_courses=4):
    conn = sqlite3.connect("college.db")
    cursor = conn.cursor()

    cursor.execute("SELECT CourseID, CourseName, Credits FROM Courses")
    all_courses = cursor.fetchall()

    cursor.execute("SELECT CourseID FROM StudentCourses WHERE StudentUSN=?", (student_usn,))
    enrolled_ids = {row[0] for row in cursor.fetchall()}
    available_courses = [c for c in all_courses if c[0] not in enrolled_ids]

    if not available_courses:
        conn.close()
        return "You are already enrolled in all available courses."

    print("\nAvailable Courses:")
    for course in available_courses:
        print(f"{course[0]}: {course[1]} ({course[2]} credits)")

    manual_choice = input("Do you want to select one course yourself first? (yes/no): ").strip().lower()
    selected = []
    total_credits = 0

    if manual_choice == "yes":
        try:
            selected_id = int(input("Enter the Course ID to select: "))
            selected_course = next((c for c in available_courses if c[0] == selected_id), None)
            if not selected_course:
                conn.close()
                return "Invalid course ID."
            selected.append(selected_course)
            total_credits += selected_course[2]
            available_courses = [c for c in available_courses if c[0] != selected_id]
        except ValueError:
            conn.close()
            return "Invalid input."

    try:
        remaining_slots = int(input(f"How many more courses do you want to pick (max {max_courses - len(selected)}): "))
    except ValueError:
        conn.close()
        return "Please enter a valid number."

    if remaining_slots < 0 or len(selected) + remaining_slots > max_courses:
        conn.close()
        return f"You can only take up to {max_courses} courses in total."

    best_combo = []
    best_credits = 0

    for combo in itertools.combinations(available_courses, remaining_slots):
        credits = sum(course[2] for course in combo)
        if total_credits + credits <= max_credits and credits > best_credits:
            best_combo = combo
            best_credits = credits

    if not best_combo and not selected:
        conn.close()
        return "No valid course combinations found within the limits."

    final_selection = selected + list(best_combo)

    for course in final_selection:
        cursor.execute("INSERT INTO StudentCourses (StudentUSN, CourseID) VALUES (?, ?)", (student_usn, course[0]))

    conn.commit()
    conn.close()

    response = f"\nBot: Student {student_usn} enrolled in {len(final_selection)} course(s):\n"
    for course in final_selection:
        response += f"{course[0]}: {course[1]} ({course[2]} credits)\n"
    response += f"Total Credits: {sum(course[2] for course in final_selection)}"
    return response
