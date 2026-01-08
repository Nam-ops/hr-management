// Tìm kiếm nhân viên

&nbsp;   function searchEmployees() {

&nbsp;       const searchTerm = document.getElementById('searchEmployee').value.toLowerCase();

&nbsp;       const department = document.getElementById('filterDepartment').value;

&nbsp;       

&nbsp;       const filtered = employees.filter(emp => {

&nbsp;           const matchSearch = emp.name.toLowerCase().includes(searchTerm) || 

&nbsp;                             emp.id.toLowerCase().includes(searchTerm) ||

&nbsp;                             emp.department.toLowerCase().includes(searchTerm);

&nbsp;           const matchDepartment = !department || emp.department === department;

&nbsp;           return matchSearch \&\& matchDepartment;

&nbsp;       });

&nbsp;       

&nbsp;       renderEmployees(filtered);

&nbsp;   }



&nbsp;   // Sửa nhân viên

&nbsp;   function editEmployee(index) {

&nbsp;       editingIndex = index;

&nbsp;       const emp = employees\[index];

&nbsp;       

&nbsp;       document.getElementById('modalTitle').textContent = 'Chỉnh Sửa Nhân Viên';

&nbsp;       document.getElementById('empId').value = emp.id;

&nbsp;       document.getElementById('empName').value = emp.name;

&nbsp;       document.getElementById('empEmail').value = emp.email;

&nbsp;       document.getElementById('empPhone').value = emp.phone;

&nbsp;       document.getElementById('empDepartment').value = emp.department;

&nbsp;       document.getElementById('empPosition').value = emp.position;

&nbsp;       document.getElementById('empBirthday').value = emp.birthday;

&nbsp;       document.getElementById('empJoinDate').value = emp.joinDate;

&nbsp;       document.getElementById('empAddress').value = emp.address;

&nbsp;       

&nbsp;       document.getElementById('employeeModal').classList.add('active');

&nbsp;   }



&nbsp;   // Xóa nhân viên

&nbsp;   function deleteEmployee(index) {

&nbsp;       if (confirm('Bạn có chắc muốn xóa nhân viên này?')) {

&nbsp;           employees.splice(index, 1);

&nbsp;           renderEmployees();

&nbsp;           updateStats();

&nbsp;           updateEmployeeSelects();

&nbsp;       }

&nbsp;   }



&nbsp;   // Cập nhật dropdown chọn nhân viên

&nbsp;   function updateEmployeeSelects() {

&nbsp;       const selects = \['attendanceEmployee', 'leaveEmployee', 'salaryEmployee'];

&nbsp;       selects.forEach(selectId => {

&nbsp;           const select = document.getElementById(selectId);

&nbsp;           select.innerHTML = '<option value="">-- Chọn nhân viên --</option>' + 

&nbsp;               employees.map((emp, idx) => `<option value="${idx}">${emp.name} (${emp.id})</option>`).join('');

&nbsp;       });

&nbsp;   }



&nbsp;   // Thêm chấm công

&nbsp;   function addAttendance() {

&nbsp;       const empIndex = document.getElementById('attendanceEmployee').value;

&nbsp;       const date = document.getElementById('attendanceDate').value;

&nbsp;       const checkIn = document.getElementById('checkInTime').value;

&nbsp;       const checkOut = document.getElementById('checkOutTime').value;

&nbsp;       

&nbsp;       if (!empIndex || !date || !checkIn || !checkOut) {

&nbsp;           alert('Vui lòng điền đầy đủ thông tin!');

&nbsp;           return;

&nbsp;       }

&nbsp;       

&nbsp;       const employee = employees\[empIndex];

&nbsp;       const hours = calculateHours(checkIn, checkOut);

&nbsp;       

&nbsp;       attendanceRecords.push({

&nbsp;           employee: employee.name,

&nbsp;           date: date,

&nbsp;           checkIn: checkIn,

&nbsp;           checkOut: checkOut,

&nbsp;           hours: hours,

&nbsp;           status: hours >= 8 ? 'Đủ giờ' : 'Thiếu giờ'

&nbsp;       });

&nbsp;       

&nbsp;       renderAttendance();

&nbsp;       updateStats();

&nbsp;       alert('Đã lưu chấm công thành công!');

&nbsp;   }



&nbsp;   // Tính số giờ làm việc

&nbsp;   function calculateHours(checkIn, checkOut) {

&nbsp;       const start = new Date('2000-01-01 ' + checkIn);

&nbsp;       const end = new Date('2000-01-01 ' + checkOut);

&nbsp;       const diff = (end - start) / (1000 \* 60 \* 60);

&nbsp;       return Math.max(0, diff).toFixed(1);

&nbsp;   }



&nbsp;   // Hiển thị lịch sử chấm công

&nbsp;   function renderAttendance() {

&nbsp;       const tbody = document.getElementById('attendanceTableBody');

&nbsp;       

&nbsp;       if (attendanceRecords.length === 0) {

&nbsp;           tbody.innerHTML = '<tr><td colspan="6" style="text-align: center; padding: 40px; color: #999;">Chưa có dữ liệu chấm công</td></tr>';

&nbsp;           return;

&nbsp;       }

&nbsp;       

&nbsp;       tbody.innerHTML = attendanceRecords.map(record => `

&nbsp;           <tr>

&nbsp;               <td>${record.date}</td>

&nbsp;               <td>${record.employee}</td>

&nbsp;               <td>${record.checkIn}</td>

&nbsp;               <td>${record.checkOut}</td>

&nbsp;               <td>${record.hours}h</td>

&nbsp;               <td><span class="badge ${record.status === 'Đủ giờ' ? 'badge-active' : 'badge-inactive'}">${record.status}</span></td>

&nbsp;           </tr>

&nbsp;       `).join('');

&nbsp;   }



&nbsp;   // Thêm đơn nghỉ phép

&nbsp;   function addLeave() {

&nbsp;       const empIndex = document.getElementById('leaveEmployee').value;

&nbsp;       const type = document.getElementById('leaveType').value;

&nbsp;       const startDate = document.getElementById('leaveStartDate').value;

&nbsp;       const endDate = document.getElementById('leaveEndDate').value;

&nbsp;       const reason = document.getElementById('leaveReason').value;

&nbsp;       

&nbsp;       if (!empIndex || !startDate || !endDate) {

&nbsp;           alert('Vui lòng điền đầy đủ thông tin!');

&nbsp;           return;

&nbsp;       }

&nbsp;       

&nbsp;       const employee = employees\[empIndex];

&nbsp;       const days = calculateLeaveDays(startDate, endDate);

&nbsp;       

&nbsp;       leaveRecords.push({

&nbsp;           employee: employee.name,

&nbsp;           type: type,

&nbsp;           startDate: startDate,

&nbsp;           endDate: endDate,

&nbsp;           days: days,

&nbsp;           reason: reason,

&nbsp;           status: 'Chờ duyệt'

&nbsp;       });

&nbsp;       

&nbsp;       renderLeave();

&nbsp;       updateStats();

&nbsp;       

&nbsp;       document.getElementById('leaveReason').value = '';

&nbsp;       alert('Đã đăng ký nghỉ phép thành công!');

&nbsp;   }



&nbsp;   // Tính số ngày nghỉ

&nbsp;   function calculateLeaveDays(start, end) {

&nbsp;       const startDate = new Date(start);

&nbsp;       const endDate = new Date(end);

&nbsp;       const diff = (endDate - startDate) / (1000 \* 60 \* 60 \* 24);

&nbsp;       return Math.max(1, diff + 1);

&nbsp;   }



&nbsp;   // Hiển thị danh sách nghỉ phép

&nbsp;   function renderLeave() {

&nbsp;       const tbody = document.getElementById('leaveTableBody');

&nbsp;       

&nbsp;       if (leaveRecords.length === 0) {

&nbsp;           tbody.innerHTML = '<tr><td colspan="8" style="text-align: center; padding: 40px; color: #999;">Chưa có đơn nghỉ phép nào</td></tr>';

&nbsp;           return;

&nbsp;       }

&nbsp;       

&nbsp;       tbody.innerHTML = leaveRecords.map((record, index) => `

&nbsp;           <tr>

&nbsp;               <td>${record.employee}</td>

&nbsp;               <td>${record.type}</td>

&nbsp;               <td>${record.startDate}</td>

&nbsp;               <td>${record.endDate}</td>

&nbsp;               <td>${record.days} ngày</td>

&nbsp;               <td>${record.reason}</td>

&nbsp;               <td><span class="badge badge-leave">${record.status}</span></td>

&nbsp;               <td>

&nbsp;                   <div class="action-btns">

&nbsp;                       <button class="btn btn-secondary" onclick="approveLeave(${index})">✅ Duyệt</button>

&nbsp;                       <button class="btn btn-danger" onclick="rejectLeave(${index})">❌ Từ chối</button>

&nbsp;                   </div>

&nbsp;               </td>

&nbsp;           </tr>

&nbsp;       `).join('');

&nbsp;   }



&nbsp;   // Duyệt nghỉ phép

&nbsp;   function approveLeave(index) {

&nbsp;       leaveRecords\[index].status = 'Đã duyệt';

&nbsp;       renderLeave();

&nbsp;   }



&nbsp;   // Từ chối nghỉ phép

&nbsp;   function rejectLeave(index) {

&nbsp;       leaveRecords\[index].status = 'Từ chối';

&nbsp;       renderLeave();

&nbsp;   }



&nbsp;   // Tính lương

&nbsp;   function calculateSalary() {

&nbsp;       const empIndex = document.getElementById('salaryEmployee').value;

&nbsp;       const month = document.getElementById('salaryMonth').value;

&nbsp;       const baseSalary = parseFloat(document.getElementById('baseSalary').value) || 0;

&nbsp;       const allowance = parseFloat(document.getElementById('allowance').value) || 0;

&nbsp;       const bonus = parseFloat(document.getElementById('bonus').value) || 0;

&nbsp;       const deduction = parseFloat(document.getElementById('deduction').value) || 0;

&nbsp;       

&nbsp;       if (!empIndex || !month || baseSalary === 0) {

&nbsp;           alert('Vui lòng điền đầy đủ thông tin!');

&nbsp;           return;

&nbsp;       }

&nbsp;       

&nbsp;       const employee = employees\[empIndex];

&nbsp;       const totalSalary = baseSalary + allowance + bonus - deduction;

&nbsp;       

&nbsp;       salaryRecords.push({

&nbsp;           month: month,

&nbsp;           employee: employee.name,

&nbsp;           baseSalary: baseSalary,

&nbsp;           allowance: allowance,

&nbsp;           bonus: bonus,

&nbsp;           deduction: deduction,

&nbsp;           total: totalSalary,

&nbsp;           status: 'Đã tính'

&nbsp;       });

&nbsp;       

&nbsp;       renderSalary();

&nbsp;       updateStats();

&nbsp;       alert(`Đã tính lương cho ${employee.name}: ${formatCurrency(totalSalary)}`);

&nbsp;   }



&nbsp;   // Hiển thị bảng lương

&nbsp;   function renderSalary() {

&nbsp;       const tbody = document.getElementById('salaryTableBody');

&nbsp;       

&nbsp;       if (salaryRecords.length === 0) {

&nbsp;           tbody.innerHTML = '<tr><td colspan="8" style="text-align: center; padding: 40px; color: #999;">Chưa có bảng lương nào</td></tr>';

&nbsp;           return;

&nbsp;       }

&nbsp;       

&nbsp;       tbody.innerHTML = salaryRecords.map(record => `

&nbsp;           <tr>

&nbsp;               <td>${record.month}</td>

&nbsp;               <td>${record.employee}</td>

&nbsp;               <td>${formatCurrency(record.baseSalary)}</td>

&nbsp;               <td>${formatCurrency(record.allowance)}</td>

&nbsp;               <td>${formatCurrency(record.bonus)}</td>

&nbsp;               <td>${formatCurrency(record.deduction)}</td>

&nbsp;               <td><strong>${formatCurrency(record.total)}</strong></td>

&nbsp;               <td><span class="badge badge-active">${record.status}</span></td>

&nbsp;           </tr>

&nbsp;       `).join('');

&nbsp;   }



&nbsp;   // Format tiền tệ

&nbsp;   function formatCurrency(amount) {

&nbsp;       return new Intl.NumberFormat('vi-VN', { style: 'currency', currency: 'VND' }).format(amount);

&nbsp;   }



&nbsp;   // Cập nhật thống kê

&nbsp;   function updateStats() {

&nbsp;       document.getElementById('totalEmployees').textContent = employees.length;

&nbsp;       document.getElementById('activeEmployees').textContent = employees.filter(e => e.status === 'active').length;

&nbsp;       

&nbsp;       const today = new Date().toISOString().slice(0, 10);

&nbsp;       const onLeave = leaveRecords.filter(r => 

&nbsp;           r.status === 'Đã duyệt' \&\& 

&nbsp;           r.startDate <= today \&\& 

&nbsp;           r.endDate >= today

&nbsp;       ).length;

&nbsp;       document.getElementById('onLeaveToday').textContent = onLeave;

&nbsp;   }



&nbsp;   // Cập nhật báo cáo

&nbsp;   function updateReports() {

&nbsp;       const totalSalary = salaryRecords.reduce((sum, r) => sum + r.total, 0);

&nbsp;       document.getElementById('totalSalaryThisMonth').textContent = formatCurrency(totalSalary);

&nbsp;       

&nbsp;       document.getElementById('totalWorkDays').textContent = attendanceRecords.length;

&nbsp;       

&nbsp;       const totalLeave = leaveRecords.filter(r => r.status === 'Đã duyệt').reduce((sum, r) => sum + r.days, 0);

&nbsp;       document.getElementById('totalLeaveDays').textContent = totalLeave;

&nbsp;       

&nbsp;       const attendanceRate = attendanceRecords.length > 0 ? 

&nbsp;           (attendanceRecords.filter(r => r.status === 'Đủ giờ').length / attendanceRecords.length \* 100).toFixed(1) : 100;

&nbsp;       document.getElementById('attendanceRate').textContent = attendanceRate + '%';

&nbsp;       

&nbsp;       renderDepartmentReport();

&nbsp;   }



&nbsp;   // Báo cáo theo phòng ban

&nbsp;   function renderDepartmentReport() {

&nbsp;       const tbody = document.getElementById('departmentReportBody');

&nbsp;       const departments = \['Kỹ Thuật', 'Kinh Doanh', 'Marketing', 'Nhân Sự', 'Kế Toán'];

&nbsp;       

&nbsp;       if (employees.length === 0) {

&nbsp;           tbody.innerHTML = '<tr><td colspan="3" style="text-align: center; padding: 40px; color: #999;">Chưa có dữ liệu</td></tr>';

&nbsp;           return;

&nbsp;       }

&nbsp;       

&nbsp;       tbody.innerHTML = departments.map(dept => {

&nbsp;           const count = employees.filter(e => e.department === dept).length;

&nbsp;           const percentage = (count / employees.length \* 100).toFixed(1);

&nbsp;           return `

&nbsp;               <tr>

&nbsp;                   <td><strong>${dept}</strong></td>

&nbsp;                   <td>${count} người</td>

&nbsp;                   <td>${percentage}%</td>

&nbsp;               </tr>

&nbsp;           `;

&nbsp;       }).join('');

&nbsp;   }



&nbsp;   // Khởi tạo

&nbsp;   updateStats();

</script>

