# 🎓 Face Recognition Attendance System

A desktop-based attendance management system built with Python that uses real-time face recognition to automatically mark student attendance. The system integrates OpenCV's LBPH face recognizer, a MySQL database, and a Tkinter GUI — and exports attendance reports to Excel automatically after each session.

---

## ✨ Features

- **Secure Login & Registration** — Email/password authentication backed by MySQL; new accounts can be registered directly from the login screen.
- **Student Management** — Add, update, delete, and search student records (name, roll no., department, course, year, semester, section, gender, DOB, contact info).
- **Face Dataset Generation** — Capture 100 face samples per student live from the webcam and store them in the `data/` folder.
- **Model Training** — Train an LBPH face recognizer on captured samples with one click; outputs `classifier.xml`.
- **Real-Time Face Recognition** — Detect and identify student faces via webcam with a configurable confidence threshold; unknown faces are highlighted separately.
- **Automatic Attendance Marking** — Recognised students are written to `Attend.csv` with timestamp and date; a 2-minute cooldown prevents duplicate entries in a single session.
- **Excel Report Generation** — After every recognition session a dated `Attendance_YYYY-MM-DD.xlsx` is auto-generated under `attendance_reports/`, colour-coding Present (green) and Absent (red) rows and including a summary header.
- **Student Master Export** — Every save/update/delete in Student Management auto-exports `student_reports/All_Students.xlsx`.
- **Attendance Management Panel** — Import any CSV, view records in a table, edit individual entries and export the updated file.

---

## 🖥️ Tech Stack

| Layer | Technology |
|---|---|
| Language | Python 3.9.1 |
| GUI | Tkinter + ttk |
| Face Detection | OpenCV Haar Cascade |
| Face Recognition | OpenCV LBPH (`opencv-contrib-python`) |
| Database | MySQL (`mysql-connector-python`) |
| Image Processing | Pillow |
| Excel Export | openpyxl |
| Numerical | NumPy |

---

## 📁 Project Structure

```
face-recognition-attendance/
│
├── run.py                         # Entry point — starts the Login window
├── login.py                       # Login & Register UI + DB auth
├── main.py                        # Main dashboard with all module buttons
│
├── student.py                     # Student CRUD + face dataset capture
├── train.py                       # Train LBPH classifier on captured images
├── face_recognition.py            # Real-time recognition + attendance marking
├── attendance.py                  # Attendance CSV viewer / editor
│
├── export_students_excel.py       # Exports All_Students.xlsx from DB
├── generate_attendance_excel.py   # Generates dated Attendance Excel after session
│
├── about.py                       # About page
├── devlopers.py                   # Developers page
│
├── haarcascade_frontalface_default.xml   # OpenCV face detection model
├── classifier.xml                 # Trained LBPH model (generated after training)
├── Attend.csv                     # Running attendance log (auto-updated)
│
├── data/                          # Face image samples (generated at runtime)
├── attendance_reports/            # Auto-generated dated attendance Excel files
├── student_reports/               # Auto-generated student master Excel file
├── images/                        # UI assets (logos, backgrounds, icons)
│
└── requirements.txt
```

---

## ⚙️ Prerequisites

- Python **3.9.1** (other 3.x versions may work but are untested)
- MySQL Server running locally
- A working webcam

---

## 🚀 Setup & Installation

### 1. Clone the repository

```bash
git clone https://github.com/your-username/face-recognition-attendance.git
cd face-recognition-attendance
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure MySQL

Create the database and required tables. Open a MySQL prompt and run:

```sql
CREATE DATABASE face_recognizer;
USE face_recognizer;

CREATE TABLE student (
    studentID     INT PRIMARY KEY,
    dep           VARCHAR(100),
    course        VARCHAR(100),
    year          VARCHAR(50),
    semester      VARCHAR(50),
    student_name  VARCHAR(100),
    section       VARCHAR(20),
    Roll_no       VARCHAR(50),
    gender        VARCHAR(20),
    dob           VARCHAR(30),
    email         VARCHAR(100),
    Phone_no      VARCHAR(20),
    address       VARCHAR(255),
    teacher_name  VARCHAR(100),
    take_photo    VARCHAR(10)
);

CREATE TABLE users (
    id       INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL,
    email    VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(100) NOT NULL
);
```

### 4. Update database credentials

If your MySQL credentials differ from the defaults, update them in the following files:

| File | Variable(s) to change |
|---|---|
| `login.py` | `DB_HOST`, `DB_USER`, `DB_PASSWORD`, `DB_NAME` |
| `student.py` | connection strings inside methods |
| `face_recognition.py` | connection string inside `face_recog()` |
| `export_students_excel.py` | `DB_CONFIG` dict |
| `generate_attendance_excel.py` | `DB_CONFIG` dict |

> Default credentials are `root` / `admin` on `localhost`.

### 5. Prepare required directories

```bash
mkdir data attendance_reports student_reports images
```

Place your UI image assets inside the `images/` folder (see filenames referenced in the source files).

### 6. Run the application

```bash
python run.py
```

---

## 📖 Usage Workflow

```
1. Register / Login
        ↓
2. Student Details  →  Add students & capture 100 face samples per student
        ↓
3. Train Data       →  Train the LBPH classifier (creates classifier.xml)
        ↓
4. Face Detector    →  Start webcam; attendance is marked automatically
        ↓
5. Attendance Details  →  View, edit, import/export the attendance CSV
```

### Step-by-step

1. **Register an account** on the login screen, then sign in.
2. Open **Student Details**, fill in the student form, and click **Generate Dataset** — the webcam will capture 100 face images automatically.
3. Open **Train Data** and click **TRAIN DATA**. Wait for the success message.
4. Open **Face Detector** and click **FACE DETECTOR**. The webcam window will open. Recognised students will have their names displayed and attendance will be marked in `Attend.csv`. Press **Enter** to stop.
5. An Excel report (`attendance_reports/Attendance_YYYY-MM-DD.xlsx`) is generated automatically when the session ends.
6. Open **Attendance Details** to import the CSV, review records, update statuses, and export as needed.

---

## 📊 Excel Reports

### Attendance Report (`attendance_reports/Attendance_YYYY-MM-DD.xlsx`)

| Column | Description |
|---|---|
| S.No | Serial number |
| Student ID | Unique student ID |
| Roll No | Roll number |
| Student Name | Full name |
| Department | Department |
| Section | Class section |
| Semester | Current semester |
| Attendance Status | **Present** (green) / **Absent** (red) |

A summary row at the top shows total, present, and absent counts for the day.

### Student Master (`student_reports/All_Students.xlsx`)

Full student table exported from the database, auto-refreshed on every save/update/delete operation.

---

## ⚠️ Known Limitations

- Passwords are stored in plain text — this is a desktop/lab project and is **not suitable for production** without hashing (e.g., bcrypt).
- Recognition accuracy depends heavily on lighting conditions and the quality/variety of training images.
- The `Attend.csv` file must exist before the first recognition session (a blank file is fine).
- Image paths in several modules are hardcoded to the `images/` folder; ensure all asset files are present before running.

---

## 📦 Requirements

```
mysql-connector-python  9.4.0
numpy                   2.0.2
opencv-contrib-python   4.13.0.92
openpyxl                3.1.5
Pillow                  8.1.0

Python version: 3.9.1
```

Install with:
```bash
pip install mysql-connector-python numpy opencv-contrib-python openpyxl Pillow
```

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

---

## 📄 License

This project is intended for educational purposes. Feel free to use and modify it for academic or personal projects.

---

## 👨‍💻 Developers

Developed at **KIIT** — see the **Developers** section inside the application for full credits.
