# resume-analyzer-project  //http://127.0.0.1:5000

<!DOCTYPE html>
<html>
<head>
<title>AI Resume Analyzer</title>

<link href="https://fonts.googleapis.com/css2?family=Poppins&display=swap" rel="stylesheet">

<style>
body {
    font-family: 'Poppins', sans-serif;
    margin: 0;
    background: linear-gradient(135deg,#1e3c72,#2a5298);
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

/* Card */
.container {
    background: white;
    padding: 40px;
    width: 500px;
    border-radius: 20px;
    box-shadow: 0 20px 40px rgba(0,0,0,0.3);
    text-align: center;
}

/* Title */
h1 {
    margin-bottom: 20px;
}

/* Button */
button {
    background: linear-gradient(to right,#ff416c,#ff4b2b);
    color: white;
    padding: 12px 25px;
    border: none;
    border-radius: 30px;
    cursor: pointer;
}

button:hover {
    transform: scale(1.05);
}

/* Skills */
.skill {
    background: #2a5298;
    color: white;
    padding: 6px 12px;
    border-radius: 20px;
    margin: 5px;
    display: inline-block;
}

/* Cards */
.card {
    background: #f1f3f8;
    padding: 12px;
    border-radius: 10px;
    margin-top: 10px;
}

/* Progress */
.progress-bar {
    background: #ddd;
    border-radius: 20px;
}

.progress {
    height: 20px;
    color: white;
    text-align: center;
    border-radius: 20px;
}

/* Error */
.error {
    color: red;
}
</style>

</head>

<body>

<div class="container">

<h1>🚀 AI Resume Analyzer</h1>

<form method="POST" enctype="multipart/form-data">
<input type="file" name="resume" required><br><br>
<button>Analyze Resume</button>
</form>

{% if error %}
<p class="error">{{ error }}</p>
{% endif %}

{% if skills %}

<h3>🧠 Skills</h3>
{% for s in skills %}
<span class="skill">{{ s }}</span>
{% endfor %}

<h3>💼 Job</h3>
<div class="card">{{ job }}</div>

<h3>⭐ Score</h3>
<div class="progress-bar">
<div class="progress" style="width:{{score}}%;background:blue">{{score}}%</div>
</div>

<h3>🎯 Match</h3>
<div class="progress-bar">
<div class="progress" style="width:{{match}}%;background:green">{{match}}%</div>
</div>

<h3>⚠ Missing Skills</h3>
{% for m in missing %}
<span class="skill" style="background:red">{{ m }}</span>
{% endfor %}

{% endif %}

</div>

</body>
</html>

//http://127.0.0.1:5000



//app.py
from flask import Flask, render_template, request
import PyPDF2
import spacy
import os

app = Flask(__name__)
app.config['UPLOAD_FOLDER'] = 'uploads'

# Ensure uploads folder exists
if not os.path.exists('uploads'):
    os.makedirs('uploads')

nlp = spacy.load("en_core_web_sm")

# Extract text safely
def extract_text(file_path):
    text = ""
    try:
        reader = PyPDF2.PdfReader(file_path)
        for page in reader.pages:
            content = page.extract_text()
            if content:
                text += content
    except:
        return "ERROR"
    return text

# Skill extraction
def analyze_resume(text):
    keywords = ["python", "java", "sql", "Cloud Computing","data analyst", "data science"]
    skills = []

    for word in text.lower().split():
        if word in keywords:
            skills.append(word.capitalize())

    return list(set(skills))

# Score
def resume_score(skills):
    return min(len(skills) * 20, 100)

# Job recommendation
def recommend_job(skills):
    s = [i.lower() for i in skills]
    if "python" in s:
        return "Software Developer / Data Scientist"
    elif "java" in s:
        return "Backend Developer"
    else:
        return "General IT Role"

# Job match %
def job_match(skills):
    required = ["python", "sql", "machine learning"]
    s = [i.lower() for i in skills]
    match = sum(1 for i in required if i in s)
    return int((match / len(required)) * 100)

# Missing skills
def missing_skills(skills):
    required = ["python", "sql", "machine learning", "data science"]
    s = [i.lower() for i in skills]
    return [i for i in required if i not in s]

@app.route("/", methods=["GET", "POST"])
def index():
    skills = []
    job = ""
    score = 0
    match = 0
    missing = []

    if request.method == "POST":
        file = request.files.get("resume")

        if file and file.filename != "":
            path = os.path.join(app.config['UPLOAD_FOLDER'], file.filename)
            file.save(path)

            text = extract_text(path)

            if text == "ERROR":
                return render_template("index.html", error="Invalid PDF file")

            skills = analyze_resume(text)
            job = recommend_job(skills)
            score = resume_score(skills)
            match = job_match(skills)
            missing = missing_skills(skills)

    return render_template("index.html",
                           skills=skills,
                           job=job,
                           score=score,
                           match=match,
                           missing=missing,
                           error=None)

if __name__ == "__main__":
    app.run(debug=True)
