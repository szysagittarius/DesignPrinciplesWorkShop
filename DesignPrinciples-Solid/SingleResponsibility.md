
## Single Responsibility 
every software component should have one and only one responsibility
- component can be a class, a methods or a module

### Responsibility - Cohesion 
Cohesion is the degree to which the various parts of a software component are related
- low cohesion methods, or not that closed related function should be abstacted out to another class
- if we see the internal methods can be reused somewhere else, it means low cohesion

```
public class Square {
    int side = 5;

    public int calculateArea() {
        return side * side;
    }

    public int calculatePerimeter() {
        return side * 4;
    }

    public void draw() {
        if (highResolutionMonitor) {
            // Render a high resolution image of a square
        } else {
            // Render a normal image of a square
        }
    }

    public void rotate(int degree) {
        // Rotate the image of the square clockwise to
        // the required degree and re-render
    }
}

```

Refactor
```
public class Square {
    int side = 5;

    public int calculateArea() {
        return side * side;
    }

    public int calculatePerimeter() {
        return side * 4;
    }
}


public class SquareUI {
    public void draw() {
        if (highResolutionMonitor) {
            // Render a high resolution image of a square
        } else {
            // Render a normal image of a square
        }
    }

    public void rotate(int degree) {
        // Rotate the image of the square clockwise to
        // the required degree and re-render
    }
}


```

### Responsibility - Coupling
Coupling is defined as the level of interdependency between various software components

```
public class Student {

    private String studentId;
    private Date studentDoB;
    private String address;

    public void save() {
        // Serialize object into a string representation
        String objectStr = MyUtils.serializeIntoAString(this);
        Connection connection = null;
        Statement stmt = null;
        try {
            Class.forName("com.mysql.jdbc.Driver");
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/MyDB", "root", "password");
            stmt = connection.createStatement();
            stmt.execute("INSERT INTO STUDENT VALUES (" + objectStr + ")");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public String getStudentId() {
        return studentId;
    }

    public void setStudentId(String studentId) {
        this.studentId = studentId;
    }
}
```
Refactor
```
public class Student {
    private String studentId;
    private Date studentDOB;
    private String address;

    public void save() {
        new StudentRepository().save(this);
    }

    public String getStudentId() {
        return studentId;
    }

    public void setStudentId(String studentId) {
        this.studentId = studentId;
    }
}

=>
Responsibility: Handle core student profile data" 

public class StudentRepository {
    public void save(Student student) {
        // Serialize object into a string representation
        String objectStr = MyUtils.serializeIntoAString(student);
        Connection connection = null;
        Statement stmt = null;
        try {
            Class.forName("com.mysql.jdbc.Driver");
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/MyDB", "root", "password");
            stmt = connection.createStatement();
            stmt.execute("INSERT INTO STUDENT VALUES (" + objectStr + ")");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

=>
"Loose Coupling helps attain better adherence to the Single Responsibility Principle

```


## Responsibility - Summary
Cohesion

    Text: "Cohesion is the degree to which the various parts of a software component are related."
    Guidance: Aim for High Cohesion ✔️

Coupling

    Text: "Coupling is defined as the level of interdependency between various software components."
    Guidance: Aim for Loose Coupling ✔️


## Reason to change

Every software component should have one and only one responsibility/reason to change

```
public class Student {
    private String studentId;
    private Date studentDOB;
    private String address;

    public void save() {
        // Serialize object into a string representation
        String objectStr = MyUtils.serializeIntoAString(this);
        Connection connection = null;
        Statement stmt = null;
        try {
            Class.forName("com.mysql.jdbc.Driver");
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/MyDB", "root", "password");
            stmt = connection.createStatement();
            stmt.execute("INSERT INTO STUDENT VALUES (" + objectStr + ")");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public String getStudentId() {
        return studentId;
    }

    public void setStudentId(String studentId) {
        this.studentId = studentId;
    }
}


# Reasons to change

1. A change in the student id format.
2. A change in the student address.
3. A change in the database backend, as advised by the tech team.
```

## Refactor - Lets remove reason to change

```
public class Student {
    private String studentId;
    private Date studentDOB;
    private String address;

    public void save() {
        new StudentRepository().save(this);
    }

    public String getStudentId() {
        return studentId;
    }

    public void setStudentId(String studentId) {
        this.studentId = studentId;
    }
}


Reasons to Change for Student
- Changes to student profile
- if the reason is closely related to one and another, you can combine


public class StudentRepository {
    public void save(Student student) {
        // Serialize object into a string representation
        String objectStr = MyUtils.serializeIntoAString(student);
        Connection connection = null;
        Statement stmt = null;
        try {
            Class.forName("com.mysql.jdbc.Driver");
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/MyDB", "root", "password");
            stmt = connection.createStatement();
            stmt.execute("INSERT INTO STUDENT VALUES (" + objectStr + ")");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

Reasons to Change for StudentRepository
- A change in the database backend, as advised by the technical team.

```

