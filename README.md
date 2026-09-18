<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบเช็คชื่อนักเรียนออนไลน์</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Prompt:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>
    <style>
        body { font-family: 'Prompt', sans-serif; }
        .glass-card {
            background: rgba(255, 255, 255, 0.98);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(226, 232, 240, 0.8);
        }
        .pulse-slow {
            animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
        }
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: .5; }
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col justify-between">

    <!-- Header Section -->
    <header id="main-header" class="bg-slate-900 text-white shadow-lg sticky top-0 z-40 border-b border-slate-800">
        <div class="max-w-7xl mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center space-x-3 cursor-pointer" onclick="switchTab('dashboard')">
                <div class="bg-gradient-to-tr from-blue-600 to-indigo-600 p-2.5 rounded-xl text-white shadow-md flex items-center justify-center">
                    <i class="fa-solid fa-qrcode text-xl"></i>
                </div>
                <div>
                    <h1 class="font-bold text-lg leading-tight flex items-center">
                        Smart Check-In
                        <span class="ml-2 text-[10px] bg-emerald-500/20 text-emerald-400 border border-emerald-500/30 font-semibold px-2 py-0.5 rounded-full">Ready</span>
                    </h1>
                    <p class="text-xs text-slate-400">ระบบเช็คชื่อนักเรียนออนไลน์</p>
                </div>
            </div>
            
            <div id="teacher-nav" class="hidden md:flex space-x-2 text-sm font-medium">
                <button onclick="switchTab('dashboard')" class="px-4 py-2 rounded-lg bg-slate-800 text-blue-400">
                    <i class="fa-solid fa-chalkboard-user mr-1.5"></i>เปิดคาบเรียน
                </button>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="flex-1 max-w-xl w-full mx-auto p-4 flex flex-col justify-center">

        <!-- STUDENT CHECK-IN CARD -->
        <div id="student-view" class="w-full my-auto py-4">
            <div class="text-center mb-6">
                <div class="inline-flex p-3.5 bg-blue-600 text-white rounded-2xl shadow-lg shadow-blue-500/30 mb-3">
                    <i class="fa-solid fa-qrcode text-3xl"></i>
                </div>
                <h1 class="text-2xl font-bold text-slate-800">ระบบเช็คชื่อนักเรียนออนไลน์</h1>
                <p class="text-xs text-slate-500 mt-1">เลือกชื่อของคุณและขอรับรหัส OTP เพื่อเช็คชื่อ</p>
            </div>

            <div class="glass-card rounded-3xl shadow-xl p-6 text-center border-t-4 border-blue-600 relative overflow-hidden">
                
                <!-- Loading State -->
                <div id="student-state-loading" class="py-8 space-y-3">
                    <i class="fa-solid fa-circle-notch animate-spin text-4xl text-blue-600"></i>
                    <h3 class="text-base font-bold text-slate-700">กำลังเข้าสู่ระบบเช็คชื่อ...</h3>
                    <p class="text-xs text-slate-400">กรุณารอสักครู่ ระบบกำลังโหลดรายชื่อ</p>
                </div>

                <!-- Active Form State -->
                <div id="student-state-active" class="hidden">
                    <div class="w-14 h-14 bg-blue-50 text-blue-600 rounded-2xl flex items-center justify-center mx-auto mb-3 text-xl shadow-inner border border-blue-100">
                        <i class="fa-solid fa-user-check"></i>
                    </div>
                    <h2 class="text-lg font-bold text-slate-800" id="student-class-title">ห้องเรียนออนไลน์</h2>
                    <p class="text-xs text-slate-500 mb-6 font-medium" id="student-session-info">วิชา: เช็คชื่อเข้าเรียน</p>

                    <!-- Step 1: Select Name & Get OTP -->
                    <div id="student-step-select" class="space-y-4">
                        <div class="text-left">
                            <label class="block text-xs font-bold text-slate-700 mb-2 uppercase flex items-center">
                                <span class="w-5 h-5 rounded-full bg-blue-600 text-white flex items-center justify-center text-[10px] mr-1.5">1</span>
                                เลือกชื่อ-นามสกุลของคุณ
                            </label>
                            <select id="student-dropdown" class="w-full bg-slate-50 border border-slate-300 rounded-xl p-3.5 text-slate-800 font-medium focus:ring-2 focus:ring-blue-500 focus:outline-none shadow-sm text-sm">
                                <option value="">-- กำลังโหลดรายชื่อนักเรียน --</option>
                            </select>
                        </div>

                        <button onclick="requestStudentOTP()" class="w-full bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-700 hover:to-indigo-700 active:scale-95 text-white font-semibold py-3.5 px-4 rounded-xl shadow-lg shadow-blue-500/30 transition duration-200 flex items-center justify-center space-x-2 text-sm">
                            <i class="fa-solid fa-key"></i>
                            <span>ขอรับรหัส OTP 6 หลัก</span>
                        </button>
                    </div>

                    <!-- Step 2: Display OTP Code -->
                    <div id="student-step-otp" class="hidden mt-4 bg-slate-900 text-white rounded-2xl p-6 shadow-xl relative overflow-hidden text-center">
                        <span class="inline-flex items-center bg-amber-500/20 text-amber-300 text-xs px-3 py-1 rounded-full font-medium mb-3 border border-amber-500/30">
                            <i class="fa-solid fa-circle-notch animate-spin mr-1.5"></i>ส่งข้อมูลเรียบร้อยแล้ว
                        </span>
                        <p class="text-xs text-slate-400">รหัส OTP เช็คชื่อของคุณคือ:</p>
                        <div class="text-4xl font-black tracking-widest text-amber-400 my-3 font-mono drop-shadow-md" id="display-otp-code">
                            ------
                        </div>
                        <p class="text-[11px] text-slate-400">แจ้งรหัสนี้แก่คุณครูเพื่อยืนยันการเข้าเรียน</p>
                    </div>

                    <!-- Step 3: Success State -->
                    <div id="student-step-success" class="hidden mt-4 bg-emerald-50 text-emerald-900 rounded-2xl p-6 border border-emerald-200 shadow-md text-center space-y-2">
                        <div class="w-16 h-16 bg-emerald-500 text-white rounded-full flex items-center justify-center mx-auto text-2xl shadow-lg shadow-emerald-500/30">
                            <i class="fa-solid fa-check text-3xl"></i>
                        </div>
                        <h3 class="font-bold text-xl text-emerald-800">เช็คชื่อสำเร็จแล้ว! 🟢</h3>
                        <p class="text-xs text-emerald-700 font-medium">คุณครูยืนยันการเข้าเรียนเรียบร้อยแล้ว</p>
                    </div>
                </div>

            </div>
        </div>

    </main>

    <footer class="text-center py-4 text-xs text-slate-400">
        Smart Check-In System &copy; 2026
    </footer>

    <!-- Logic Script -->
    <script>
        const CLOUD_DB_BASE_URL = "https://checkin-realtime-default-rtdb.asia-southeast1.firebasedatabase.app";
        
        // Mock Students Data
        const defaultStudents = [
            { id: 's1', no: 1, stdId: '10001', name: 'นายกิตติพงษ์ วงศ์สว่าง' },
            { id: 's2', no: 2, stdId: '10002', name: 'นางสาวจิราพร แสงทอง' },
            { id: 's3', no: 3, stdId: '10003', name: 'นายณัฐพงษ์ ใจดี' },
            { id: 's4', no: 4, stdId: '10004', name: 'นางสาวธนิดา สมบูรณ์' },
            { id: 's5', no: 5, stdId: '10005', name: 'นายปัญญาวุฒิ สุขเสริฐ' }
        ];

        let currentSessionId = "DEFAULT_SESSION";
        let selectedStudentId = null;

        window.addEventListener('DOMContentLoaded', () => {
            const urlParams = new URLSearchParams(window.location.search);
            const sessionParam = urlParams.get('session');
            
            if (sessionParam) {
                currentSessionId = sessionParam;
            }

            initStudentView();
        });

        async function initStudentView() {
            const loadingState = document.getElementById('student-state-loading');
            const activeState = document.getElementById('student-state-active');
            const dropdown = document.getElementById('student-dropdown');

            try {
                // Fetch active session or create fallback
                const res = await fetch(`${CLOUD_DB_BASE_URL}/activeSessions/${currentSessionId}.json`);
                let data = await res.json();

                if (!data || !data.students) {
                    // Auto-initialize session if not found so student can check-in immediately
                    const sessionStudents = {};
                    defaultStudents.forEach(s => {
                        sessionStudents[s.id] = { ...s, status: 'absent', otp: '' };
                    });
                    
                    data = {
                        sessionId: currentSessionId,
                        className: "ม.4/1",
                        subject: "เช็คชื่อเข้าเรียน",
                        active: true,
                        students: sessionStudents
                    };

                    await fetch(`${CLOUD_DB_BASE_URL}/activeSessions/${currentSessionId}.json`, {
                        method: 'PUT',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(data)
                    });
                }

                // Render UI
                document.getElementById('student-class-title').textContent = `ห้อง ${data.className || 'ทั่วไป'}`;
                document.getElementById('student-session-info').textContent = `วิชา: ${data.subject || 'เช็คชื่อเข้าเรียน'}`;

                const studentsList = Object.values(data.students).sort((a, b) => a.no - b.no);
                dropdown.innerHTML = '<option value="">-- เลือกชื่อของคุณ --</option>' + 
                    studentsList.map(s => `<option value="${s.id}">${s.no}. ${s.name} (${s.stdId})</option>`).join('');

                loadingState.classList.add('hidden');
                activeState.classList.remove('hidden');

            } catch (error) {
                console.error("Initialization Error:", error);
                // Fallback to local default data if network error occurs
                dropdown.innerHTML = '<option value="">-- เลือกชื่อของคุณ --</option>' + 
                    defaultStudents.map(s => `<option value="${s.id}">${s.no}. ${s.name} (${s.stdId})</option>`).join('');
                
                loadingState.classList.add('hidden');
                activeState.classList.remove('hidden');
            }
        }

        async function requestStudentOTP() {
            const dropdown = document.getElementById('student-dropdown');
            const stdId = dropdown.value;

            if (!stdId) {
                Swal.fire({
                    icon: 'warning',
                    title: 'กรุณาเลือกชื่อ',
                    text: 'โปรดเลือกชื่อ-นามสกุลของคุณก่อนกดขอรับรหัส',
                    confirmButtonColor: '#2563eb'
                });
                return;
            }

            selectedStudentId = stdId;
            // Generate 6-digit OTP in sequence/random format
            const generatedOTP = Math.floor(100000 + Math.random() * 900000).toString();

            try {
                // Update student status to 'pending' with OTP in Cloud
                await fetch(`${CLOUD_DB_BASE_URL}/activeSessions/${currentSessionId}/students/${stdId}.json`, {
                    method: 'PATCH',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        status: 'pending',
                        otp: generatedOTP,
                        timestamp: new Date().toISOString()
                    })
                });

                document.getElementById('student-step-select').classList.add('hidden');
                document.getElementById('student-step-otp').classList.remove('hidden');
                document.getElementById('display-otp-code').textContent = generatedOTP;

                // Start polling to check if teacher approved
                setInterval(checkApprovalStatus, 2000);

            } catch (e) {
                // Show OTP directly even if cloud write fails
                document.getElementById('student-step-select').classList.add('hidden');
                document.getElementById('student-step-otp').classList.remove('hidden');
                document.getElementById('display-otp-code').textContent = generatedOTP;
            }
        }

        async function checkApprovalStatus() {
            if (!selectedStudentId) return;
            try {
                const res = await fetch(`${CLOUD_DB_BASE_URL}/activeSessions/${currentSessionId}/students/${selectedStudentId}.json`);
                const studentData = await res.json();

                if (studentData && studentData.status === 'present') {
                    document.getElementById('student-step-otp').classList.add('hidden');
                    document.getElementById('student-step-success').classList.remove('hidden');
                }
            } catch (e) {
                console.error(e);
            }
        }
    </script>
</body>
</html>
