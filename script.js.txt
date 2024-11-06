// تحميل قائمة المرضى من LocalStorage عند تحميل الصفحة
window.onload = function() {
    const savedPatients = JSON.parse(localStorage.getItem("patients")) || [];
    savedPatients.forEach((patient, index) => {
        addPatientToList(patient.name, patient.surname, patient.birthdate, patient.appointmentType, patient.completed, patient.absent, index + 1);
    });
};

// دالة لإضافة مريض إلى القائمة وعرضه مع الترقيم
function addPatientToList(name, surname, birthdate, appointmentType, completed, absent, index) {
    const listItem = document.createElement("li");
    listItem.id = `patient-${index}`; // تصحيح استخدام المعرف
    listItem.className = completed ? "completed" : absent ? "absent" : "";
    listItem.innerHTML = `
        <strong>المريض ${index}:</strong><br>
        <strong>الاسم:</strong> ${name}<br>
        <strong>اللقب:</strong> ${surname}<br>
        <strong>تاريخ الميلاد:</strong> ${birthdate}<br>
        <strong>نوع الموعد:</strong> ${appointmentType}<br>
        <button class="completeButton" onclick="completeAppointment(${index})" ${completed || absent ? 'disabled' : ''}>
            ${completed ? 'تم إتمام الموعد' : 'إتمام الموعد'}
        </button>
        <button class="absentButton" onclick="markAbsent(${index})" ${completed || absent ? 'disabled' : ''}>
            ${absent ? 'غياب مريض' : 'تسجيل غياب'}
        </button>
        <button class="deleteButton" onclick="deletePatient(${index})">
            حذف مريض
        </button>
    `;
    document.getElementById("patientList").appendChild(listItem);
}

// دالة لحذف مريض من القائمة
function deletePatient(index) {
    const password = prompt("أدخل كلمة المرور لإزالة المريض:");

    if (password === "ss") {
        const patients = JSON.parse(localStorage.getItem("patients")) || [];
        patients.splice(index - 1, 1);
        localStorage.setItem("patients", JSON.stringify(patients));

        document.getElementById("patientList").innerHTML = "";
        patients.forEach((patient, index) => {
            addPatientToList(patient.name, patient.surname, patient.birthdate, patient.appointmentType, patient.completed, patient.absent, index + 1);
        });

        alert("تم حذف المريض بنجاح.");
    } else {
        alert("كلمة المرور غير صحيحة. لا يمكنك حذف المريض.");
    }
}

// حفظ بيانات الحجز عند إرسال النموذج
document.getElementById("bookingForm").addEventListener("submit", function(event) {
    event.preventDefault();

    const name = document.getElementById("name").value;
    const surname = document.getElementById("surname").value;
    const birthdate = document.getElementById("birthdate").value;
    const appointmentType = document.getElementById("appointmentType").value;

    if (name && surname && birthdate && appointmentType) {
        const patient = { name, surname, birthdate, appointmentType, completed: false, absent: false };
        const patients = JSON.parse(localStorage.getItem("patients")) || [];
        patients.push(patient);
        localStorage.setItem("patients", JSON.stringify(patients));

        addPatientToList(name, surname, birthdate, appointmentType, false, false, patients.length);
        document.getElementById("bookingForm").reset();
    } else {
        alert("يرجى ملء جميع الحقول!");
    }
});

