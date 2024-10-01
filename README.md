# SQL
โจทย์ข้อที่ 1: เพิ่มข้อมูลใน student_insert_test
insert into mock_reg_db.student_insert_test (mock_reg_db.student_insert_test.tin_fname, 
mock_reg_db.student_insert_test.tin_lname, mock_reg_db.student_insert_test.tin_team, 
mock_reg_db.student_insert_test.tin_created_at)
values("Kitikawin", "Hompan", 6, current_date())

โจทย์ข้อที่ 2: อัปเดตข้อมูลของตนเองใน student_update_test
#SET SQL_SAFE_UPDATES = 0; คำสั่งใช้เมื่อติด save โหมด
UPDATE mock_reg_db.student_insert_test 
SET 
    mock_reg_db.student_insert_test.tin_created_at = CURRENT_DATE,
    mock_reg_db.student_insert_test.tin_lname = 'sadasdsad'
WHERE
    mock_reg_db.student_insert_test.tin_fname LIKE 'Kitikawin'

โจทย์ข้อที่ 3: รวมชื่อและนามสกุลเป็น Name
SELECT 
    CONCAT(mock_reg_db.student_update_test.tup_fname, ' ',
	mock_reg_db.student_update_test.tup_lname) AS RealName
FROM
    mock_reg_db.student_update_test

โจทย์ข้อที่ 4: จำนวนอำเภอและตำบลในแต่ละจังหวัด
SELECT 
    mock_thailand_db.provinces.pv_name_th,
    COUNT(mock_thailand_db.districts.dt_id),
    COUNT(mock_thailand_db.sub_districts.sdt_id)
FROM
    mock_thailand_db.provinces
JOIN
    mock_thailand_db.districts ON mock_thailand_db.provinces.pv_id = mock_thailand_db.districts.dt_pv_id
JOIN
    mock_thailand_db.sub_districts ON mock_thailand_db.districts.dt_id = mock_thailand_db.sub_districts.sdt_dt_id
GROUP BY 
	mock_thailand_db.provinces.pv_id

โจทย์ข้อที่ 5: การ join ข้าม database เพื่อตรวจสอบที่อยู่ของนิสิต
SELECT 
    CONCAT(mock_reg_db.user_students.usst_fname, ' ',
	mock_reg_db.user_students.usst_lname) AS full_name,
    mock_reg_db.user_addresses.usa_building_number AS building_number,
    mock_reg_db.user_addresses.usa_building_name AS building_name,
    mock_reg_db.user_addresses.usa_moo AS moo,
    mock_reg_db.user_addresses.usa_soi AS soi,
    mock_reg_db.user_addresses.usa_road AS road,
    mock_thailand_db.sub_districts.sdt_name_th,
    mock_thailand_db.districts.dt_name_th,
    mock_thailand_db.provinces.pv_name_th,
    mock_thailand_db.sub_districts.sdt_zip_code
FROM
    mock_reg_db.user_students
        JOIN
    mock_reg_db.user_addresses ON mock_reg_db.user_students.usst_usa_id = mock_reg_db.user_addresses.usa_id
        JOIN
    mock_thailand_db.sub_districts ON mock_reg_db.user_addresses.usa_sdt_id = mock_thailand_db.sub_districts.sdt_id
        JOIN
    mock_thailand_db.districts ON mock_thailand_db.sub_districts.sdt_dt_id = mock_thailand_db.districts.dt_id
        JOIN
    mock_thailand_db.provinces ON mock_thailand_db.districts.dt_pv_id = mock_thailand_db.provinces.pv_id

โจทย์ข้อที่ 6: ตรวจสอบห้องที่มีการเรียนการสอนในวันจันทร์สำหรับหลักสูตรที่กำหนด
SELECT 
    mock_reg_db.courses.cs_code,
    mock_reg_db.courses.cs_name,
    mock_reg_db.rooms.rm_name,
    mock_reg_db.courses_timetable.ct_time_start,
    mock_reg_db.courses_timetable.ct_time_end
FROM
    mock_reg_db.courses
        JOIN
    mock_reg_db.courses_offered ON mock_reg_db.courses.cs_id = mock_reg_db.courses_offered.co_cs_id
        JOIN
    mock_reg_db.courses_timetable ON mock_reg_db.courses_offered.co_id = mock_reg_db.courses_timetable.ct_co_id
        JOIN
    mock_reg_db.rooms ON mock_reg_db.courses_timetable.ct_rm_id = mock_reg_db.rooms.rm_id
WHERE
    mock_reg_db.courses.cs_code IN ('CS101' , 'CS101', 'CS201', 'CS202')

โจทย์ข้อที่ 7: หลักสูตรที่เปิดใน 2024 และสอนในวันจันทร์
SELECT 
	mock_reg_db.curriculums.cl_name,
	mock_reg_db.courses.cs_code,
	mock_reg_db.courses.cs_name
FROM
    mock_reg_db.curriculums
JOIN 
	mock_reg_db.courses on  mock_reg_db.curriculums.cl_id = mock_reg_db.courses.cs_cl_id
JOIN
	 mock_reg_db.courses_offered on mock_reg_db.courses.cs_id = mock_reg_db.courses_offered.co_cs_id
JOIN
	mock_reg_db.courses_timetable on mock_reg_db.courses_offered.co_id = mock_reg_db.courses_timetable.ct_co_id
Where  
	 mock_reg_db.courses_offered.co_term like 1 
AND  
     mock_reg_db.courses_offered.co_year like 2024
AND
	 mock_reg_db.courses_timetable.ct_day like "Monday"

โจทย์ข้อที่ 8: แสดงรายชื่อนิสิตที่มีอาจารย์ที่ปรึกษา
SELECT 
	mock_reg_db.user_students.usst_id,
    CONCAT(mock_reg_db.user_students.usst_fname, " ",
    mock_reg_db.user_students.usst_lname) as full_name,
    count(mock_reg_db.student_advisors.sta_ussf_id) as Advisor_count
FROM
    mock_reg_db.user_students
JOIN 
	mock_reg_db.student_advisors on mock_reg_db.user_students.usst_id = mock_reg_db.student_advisors.sta_usst_id
GROUP BY 
	mock_reg_db.user_students.usst_id

โจทย์ข้อที่ 9: หานิสิตที่ไม่มีอาจารย์ที่ปรึกษาดูแล
SELECT 
    mock_reg_db.user_students.usst_id,
    CONCAT(mock_reg_db.user_students.usst_fname, ' ', mock_reg_db.user_students.usst_lname) AS full_name
FROM 
    mock_reg_db.user_students
LEFT JOIN 
    mock_reg_db.student_advisors ON mock_reg_db.user_students.usst_id = mock_reg_db.student_advisors.sta_usst_id
WHERE 
    mock_reg_db.student_advisors.sta_usst_id IS NULL;

โจทย์ข้อที่ 10: ค้นหาจำนวนนิสิตแยกตามหลักสูตร
SELECT 
    mock_reg_db.curriculums.cl_name,
    count(mock_reg_db.courses_enrolled.ce_id)
FROM 
    mock_reg_db.curriculums
JOIN
	mock_reg_db.courses on mock_reg_db.curriculums.cl_id = mock_reg_db.courses.cs_cl_id
JOIN 
	mock_reg_db.courses_enrolled on mock_reg_db.courses.cs_id = mock_reg_db.courses_enrolled.ce_co_id
GROUP BY
	mock_reg_db.curriculums.cl_id

โจทย์ข้อที่ 11: ค้นหารายวิชาที่มีนิสิตลงทะเบียนมากกว่า 5 คน
SELECT 
    mock_reg_db.courses.cs_code,
    mock_reg_db.courses.cs_name,
    count(mock_reg_db.courses_enrolled.ce_id) as total_students
FROM 
    mock_reg_db.courses
JOIN
	mock_reg_db.courses_enrolled on mock_reg_db.courses.cs_id = mock_reg_db.courses_enrolled.ce_co_id
GROUP BY
	mock_reg_db.courses.cs_id
HAVING
	total_students > 5

โจทย์ข้อที่ 12: แสดงรายวิชาที่ไม่ได้เปิดในเทอมนี้แต่เปิดในปีที่แล้ว
SELECT 
    mock_reg_db.courses.cs_code,
    mock_reg_db.courses.cs_name
FROM 
    mock_reg_db.courses
JOIN
    mock_reg_db.courses_offered ON mock_reg_db.courses.cs_id = mock_reg_db.courses_offered.co_cs_id
WHERE
    mock_reg_db.courses_offered.co_year = YEAR(CURRENT_DATE) - 1
GROUP BY
    mock_reg_db.courses.cs_id

โจทย์ข้อที่ 13: แปลงเกรดจากตัวอักษรเป็นตัวเลข
SELECT 
    CONCAT(mock_reg_db.user_students.usst_fname, ' ', mock_reg_db.user_students.usst_lname) AS full_name,
    CASE 
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'A' THEN 4
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'B+' THEN 3.5
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'B' THEN 3
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'C+' THEN 2.5
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'C' THEN 2
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'D+' THEN 1.5
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'D' THEN 1
        ELSE 0
    END AS grade_point 
FROM 
    mock_reg_db.user_students
JOIN 
    mock_reg_db.courses_enrolled ON mock_reg_db.user_students.usst_id = mock_reg_db.courses_enrolled.ce_usst_id
JOIN
    mock_reg_db.courses_offered ON mock_reg_db.courses_enrolled.ce_co_id = mock_reg_db.courses_offered.co_id
JOIN
    mock_reg_db.courses ON mock_reg_db.courses_offered.co_cs_id = mock_reg_db.courses.cs_id
WHERE 
    mock_reg_db.courses.cs_code like 'CS101'

โจทย์ข้อที่ 14: แสดงรายชื่อนิสิตที่มีคะแนนเฉลี่ยมากกว่า 3.5
SELECT 
    CONCAT(mock_reg_db.user_students.usst_fname, ' ', mock_reg_db.user_students.usst_lname) AS full_name,
    AVG(
        CASE 
            WHEN mock_reg_db.courses_enrolled.ce_grade = 'A' THEN 4
            WHEN mock_reg_db.courses_enrolled.ce_grade = 'B+' THEN 3.5
            WHEN mock_reg_db.courses_enrolled.ce_grade = 'B' THEN 3
            WHEN mock_reg_db.courses_enrolled.ce_grade = 'C+' THEN 2.5
            WHEN mock_reg_db.courses_enrolled.ce_grade = 'C' THEN 2
            WHEN mock_reg_db.courses_enrolled.ce_grade = 'D+' THEN 1.5
            WHEN mock_reg_db.courses_enrolled.ce_grade = 'D' THEN 1
            ELSE 0
        END
    ) AS average_grade
FROM 
    mock_reg_db.user_students
JOIN 
    mock_reg_db.courses_enrolled ON mock_reg_db.user_students.usst_id = mock_reg_db.courses_enrolled.ce_usst_id



โจทย์ข้อที่ 1: เพิ่มข้อมูลใน student_insert_test
คำอธิบาย: เพิ่มข้อมูลของตนเองลงในตาราง student_insert_test

ลักษณะข้อมูลที่ได้
insert into student_insert_test (tin_fname,tin_lname,tin_team,tin_created_at) values ('boat','banana','7',now())

โจทย์ข้อที่ 2: อัปเดตข้อมูลของตนเองใน student_update_test
คำอธิบาย: อัปเดตข้อมูลที่อยู่ของตนเองในตาราง student_update_test

update stu65160334_mock_reg_db.student_update_test 
set student_update_test.tup_fname = 'ธนวันต์ UPDATE', student_update_test.tup_lname = 'สุดUPDATE' , student_update_test.tup_updated_at = now()
where stu65160334_mock_reg_db.student_update_test.tup_id = 85

โจทย์ข้อที่ 3: รวมชื่อและนามสกุลเป็น Name
คำอธิบาย: รวมชื่อและนามสกุลของนักศึกษาเป็นคอลัมน์เดียวชื่อว่า name

select  concat(student_update_test.tup_fname ,' ', student_update_test.tup_lname) as namefull
from stu65160334_mock_reg_db.student_update_test

โจทย์ข้อที่ 4: จำนวนอำเภอและตำบลในแต่ละจังหวัด
คำอธิบาย: หาจำนวนอำเภอและตำบลในแต่ละจังหวัด โดยแสดงชื่อจังหวัด จำนวนอำเภอ และจำนวนตำบล

select mock_thailand_db.provinces.pv_name_th , count(distinct mock_thailand_db.districts.dt_id) , count(distinct mock_thailand_db.sub_districts.sdt_id)
from mock_thailand_db.provinces
join mock_thailand_db.districts on pv_id = dt_pv_id
join mock_thailand_db.sub_districts on dt_id = sdt_dt_id
group by pv_id




GROUP BY 
    mock_reg_db.user_students.usst_id
HAVING 
    average_grade > 3.5;
