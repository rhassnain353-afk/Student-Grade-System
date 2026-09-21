
const PASS_PERCENTAGE = 40;

// Grade rules: upar se neeche check hote hain. "min" = minimum percentage.
const GRADE_RULES = [
  { min: 90, grade: "A+" },
  { min: 80, grade: "A" },
  { min: 70, grade: "B" },
  { min: 60, grade: "C" },
  { min: 50, grade: "D" },
  { min: 40, grade: "E" },
  { min: 0,  grade: "F" }
];

const DEFAULT_SUBJECTS = ["English", "Urdu", "Mathematics", "Physics", "Chemistry", "Computer Science"];

/* ---------- 2. HTML ELEMENTS ---------- */
const subjectList = document.getElementById("subjectList");
const emptyState  = document.getElementById("emptyState");
const errorBox    = document.getElementById("errorBox");
const resultBody  = document.getElementById("resultBody");
const ring        = document.getElementById("ring");

const studentNameInput  = document.getElementById("studentName");
const studentClassInput = document.getElementById("studentClass");
const fatherNameInput   = document.getElementById("fatherName");

/* ---------- 3. CALCULATION FUNCTIONS ---------- */

// Percentage = (obtained / total) x 100
function calculatePercentage(obtained, total) {
  return (obtained / total) * 100;
}

// Percentage se grade nikalta hai (GRADE_RULES use karke)
function calculateGrade(percentage) {
  for (const rule of GRADE_RULES) {
    if (percentage >= rule.min) return rule.grade;
  }
  return "F";
}

function isPass(percentage) {
  return percentage >= PASS_PERCENTAGE;
}

/* ---------- 4. SUBJECT ADD / REMOVE ---------- */

function addSubject(name = "", total = 100) {
  const row = document.createElement("div");
  row.className = "subject-row";
  row.innerHTML = `
    <label>Subject <input type="text" class="in-name" placeholder="e.g. Biology"></label>
    <label>Obtained <input type="number" class="in-obtained" min="0" step="any" placeholder="0"></label>
    <label>Total <input type="number" class="in-total" min="0" step="any"></label>
    <button type="button" class="btn btn-danger btn-remove">Remove</button>`;

  // .value se set kiya taake special characters safe rahein
  row.querySelector(".in-name").value = name;
  row.querySelector(".in-total").value = total;
  subjectList.appendChild(row);

  if (name === "") row.querySelector(".in-name").focus(); // custom subject: seedha likhna shuru karein
  updateEmptyState();
}

function removeSubject(row) {
  row.remove();
  updateEmptyState();
  calculateResult();
}

function updateEmptyState() {
  const hasRows = subjectList.querySelectorAll(".subject-row").length > 0;
  emptyState.classList.toggle("hidden", hasRows);
}

/* ---------- 5. VALIDATION ---------- */

// Ek row ki values parh kar object bana deta hai
function readRow(row) {
  const obtainedText = row.querySelector(".in-obtained").value.trim();
  const totalText = row.querySelector(".in-total").value.trim();
  return {
    name: row.querySelector(".in-name").value.trim(),
    obtainedText: obtainedText,
    totalText: totalText,
    obtained: Number(obtainedText),
    total: Number(totalText)
  };
}

// Sahi ho to "" wapas karta hai, warna error message
function validateSubject(d) {
  if (d.name === "") return "Subject name cannot be empty.";
  if (d.obtainedText === "" || isNaN(d.obtained)) return `Please enter obtained marks for ${d.name}.`;
  if (d.totalText === "" || isNaN(d.total) || d.total <= 0) return `Total marks for ${d.name} must be greater than 0.`;
  if (d.obtained < 0) return `Obtained marks for ${d.name} cannot be negative.`;
  if (d.obtained > d.total) return `Obtained marks for ${d.name} cannot be greater than total marks.`;
  return "";
}

function showErrors(errors) {
  errorBox.innerHTML = "";
  if (errors.length === 0) {
    errorBox.classList.add("hidden");
    return;
  }
  const ul = document.createElement("ul");
  errors.forEach(msg => {
    const li = document.createElement("li");
    li.textContent = msg;
    ul.appendChild(li);
  });
  errorBox.appendChild(ul);
  errorBox.classList.remove("hidden");
}

/* ---------- 5b. STUDENT INFO (shown on the printable result) ---------- */

function updateStudentInfo() {
  document.getElementById("printName").textContent = studentNameInput.value.trim() || "-";
  document.getElementById("printClass").textContent = studentClassInput.value.trim() || "-";
  document.getElementById("printFather").textContent = fatherNameInput.value.trim() || "-";
}

/* ---------- 6. MAIN CALCULATION ---------- */

// strict = true (Calculate button): khali marks ko bhi error ginta hai
function calculateResult(strict = false) {
  const validSubjects = [];
  const errors = [];

  subjectList.querySelectorAll(".subject-row").forEach(row => {
    const d = readRow(row);
    row.classList.remove("invalid");

    // Live typing ke waqt jis row ke marks abhi khali hain use skip karein
    if (!strict && d.obtainedText === "") return;

    const error = validateSubject(d);
    if (error) {
      errors.push(error);
      row.classList.add("invalid");
      return;                       // invalid row calculation mein shamil nahi
    }
    d.percentage = calculatePercentage(d.obtained, d.total);
    d.grade = calculateGrade(d.percentage);
    d.pass = isPass(d.percentage);
    validSubjects.push(d);
  });

  showErrors(errors);
  updateResult(validSubjects);
  updateStudentInfo();
}

// Screen par summary + table update karta hai
function updateResult(subjects) {
  let totalObtained = 0, totalMax = 0;
  subjects.forEach(s => { totalObtained += s.obtained; totalMax += s.total; });

  const hasData = subjects.length > 0;
  const percentage = hasData ? calculatePercentage(totalObtained, totalMax) : 0;
  const allPass = hasData && subjects.every(s => s.pass);   // ek bhi fail => overall FAIL

  document.getElementById("resTotal").textContent = totalMax;
  document.getElementById("resObtained").textContent = totalObtained;
  document.getElementById("resPercent").textContent = percentage.toFixed(2) + "%";
  document.getElementById("resGrade").textContent = hasData ? calculateGrade(percentage) : "-";

  const status = document.getElementById("resStatus");
  status.textContent = hasData ? (allPass ? "PASS" : "FAIL") : "-";
  status.className = hasData ? (allPass ? "pass-text" : "fail-text") : "";

  // Circular indicator
  ring.style.setProperty("--p", Math.min(percentage, 100));
  ring.className = "ring" + (hasData ? (allPass ? " pass" : " fail") : "");
  document.getElementById("ringText").textContent = Math.round(percentage) + "%";

  renderTable(subjects);
}

function renderTable(subjects) {
  resultBody.innerHTML = "";

  if (subjects.length === 0) {
    const tr = document.createElement("tr");
    const td = document.createElement("td");
    td.colSpan = 6;
    td.textContent = "No results yet. Enter marks above.";
    tr.appendChild(td);
    resultBody.appendChild(tr);
    return;
  }

  subjects.forEach(s => {
    const tr = document.createElement("tr");
    const cells = [s.name, s.obtained, s.total, s.percentage.toFixed(2) + "%", s.grade, s.pass ? "Pass" : "Fail"];
    cells.forEach((text, i) => {
      const td = document.createElement("td");
      td.textContent = text;
      if (i === 5) td.className = s.pass ? "pass-text" : "fail-text";
      tr.appendChild(td);
    });
    resultBody.appendChild(tr);
  });
}

/* ---------- 7. RESET, PRINT, DARK MODE ---------- */

function resetCalculator() {
  studentNameInput.value = "";
  studentClassInput.value = "";
  fatherNameInput.value = "";
  subjectList.innerHTML = "";
  DEFAULT_SUBJECTS.forEach(name => addSubject(name, 100));
  showErrors([]);
  calculateResult();
}

function printResult() {
  window.print();   // CSS (@media print) sirf result section print karta hai
}

function toggleDarkMode() {
  const isDark = document.body.classList.toggle("dark");
  const btn = document.getElementById("themeBtn");
  btn.textContent = isDark ? "☀️ Light Mode" : "🌙 Dark Mode";
  btn.setAttribute("aria-pressed", isDark);
}

/* ---------- 8. EVENTS (buttons + live calculation) ---------- */
document.getElementById("addBtn").addEventListener("click", () => addSubject());
document.getElementById("calcBtn").addEventListener("click", () => calculateResult(true));
document.getElementById("resetBtn").addEventListener("click", resetCalculator);
document.getElementById("printBtn").addEventListener("click", printResult);
document.getElementById("themeBtn").addEventListener("click", toggleDarkMode);

// LIVE CALCULATION: kisi bhi input mein typing par result update
subjectList.addEventListener("input", () => calculateResult());

// Student info: turant printable area par update ho (marks calculate kiye bina bhi)
[studentNameInput, studentClassInput, fatherNameInput].forEach(input => {
  input.addEventListener("input", updateStudentInfo);
});

// Remove buttons: event delegation (naye rows ke liye bhi kaam karta hai)
subjectList.addEventListener("click", (event) => {
  const btn = event.target.closest(".btn-remove");
  if (btn) removeSubject(btn.closest(".subject-row"));
});

/* ---------- 9. START ---------- */
resetCalculator();
