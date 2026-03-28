import tkinter as tk
from tkinter import ttk, messagebox, filedialog
import json
import os
from datetime import datetime


class StudentGradesSystem:
    def __init__(self, root):
        self.root = root
        self.root.title("إدارة غرب الزقازيق التعليمية - نظام درجات الطلبة")
        self.root.geometry("1300x800")
        self.root.configure(bg="#1a1a2e")
        self.root.state('zoomed')

        self.students = []

        # ???????????? تعريف المواد ????????????
        self.subjects = [
            {"name": "اللغة العربية",     "max": 100, "pass": 50},
            {"name": "اللغة الانجليزية",  "max": 100, "pass": 50},
            {"name": "الدراسات",          "max": 100, "pass": 50},
            {"name": "العلوم",            "max": 80,  "pass": 40},
            {"name": "الرياضيات",         "max": 100, "pass": 50},
            {"name": "التربية الدينية",   "max": 50,  "pass": 25},
        ]
        self.total_max = sum(s["max"] for s in self.subjects)  # 530

        self.setup_styles()
        self.setup_ui()
        self.load_data()

    # ??????????????????????????????????????????
    #             إعداد الأنماط
    # ??????????????????????????????????????????
    def setup_styles(self):
        self.style = ttk.Style()
        self.style.theme_use('clam')

        self.style.configure("Title.TLabel",
                             font=("Arial", 22, "bold"),
                             background="#16213e", foreground="#e94560")
        self.style.configure("SubTitle.TLabel",
                             font=("Arial", 16),
                             background="#16213e", foreground="#ffffff")
        self.style.configure("Treeview",
                             font=("Arial", 11),
                             rowheight=35,
                             background="#0f3460",
                             foreground="white",
                             fieldbackground="#0f3460")
        self.style.configure("Treeview.Heading",
                             font=("Arial", 11, "bold"),
                             background="#16213e",
                             foreground="white")
        self.style.map("Treeview",
                       background=[("selected", "#e94560")])

    # ??????????????????????????????????????????
    #           بناء واجهة المستخدم
    # ??????????????????????????????????????????
    def setup_ui(self):
        # ??? العنوان ???
        title_frame = tk.Frame(self.root, bg="#16213e", pady=12)
        title_frame.pack(fill=tk.X)

        tk.Label(title_frame,
                 text="??  إدارة غرب الزقازيق التعليمية",
                 font=("Arial", 22, "bold"),
                 bg="#16213e", fg="#e94560").pack()
        tk.Label(title_frame,
                 text="نظام إدارة درجات الطلبة",
                 font=("Arial", 15),
                 bg="#16213e", fg="#ffffff").pack()

        # ??? إطار رئيسي ???
        main_frame = tk.Frame(self.root, bg="#1a1a2e")
        main_frame.pack(fill=tk.BOTH, expand=True, padx=10, pady=5)

        # ???????????? إدخال البيانات ????????????
        input_frame = tk.LabelFrame(
            main_frame,
            text="  ??  إدخال بيانات الطالب  ",
            font=("Arial", 14, "bold"),
            bg="#16213e", fg="#00d2ff",
            labelanchor="ne", padx=15, pady=10
        )
        input_frame.pack(fill=tk.X, pady=5)

        # اسم الطالب
        name_row = tk.Frame(input_frame, bg="#16213e")
        name_row.pack(fill=tk.X, pady=8)

        tk.Label(name_row, text="??  اسم الطالب :",
                 font=("Arial", 13, "bold"),
                 bg="#16213e", fg="#ffffff").pack(side=tk.RIGHT, padx=5)

        self.name_entry = tk.Entry(name_row,
                                   font=("Arial", 14),
                                   width=35, justify="right",
                                   bg="#0f3460", fg="white",
                                   insertbackground="white",
                                   relief="flat", bd=2)
        self.name_entry.pack(side=tk.RIGHT, padx=5, ipady=4)

        # ??? حقول الدرجات ???
        grades_row = tk.Frame(input_frame, bg="#16213e")
        grades_row.pack(fill=tk.X, pady=8)

        self.grade_entries = {}
        colors = ["#e94560", "#533483", "#0f3460", "#16813d", "#e97d00", "#8b0000"]

        for i, subject in enumerate(self.subjects):
            frame = tk.Frame(grades_row, bg="#16213e",
                             highlightbackground=colors[i],
                             highlightthickness=2, padx=8, pady=5)
            frame.pack(side=tk.RIGHT, padx=4)

            tk.Label(frame,
                     text=f"{subject['name']}",
                     font=("Arial", 10, "bold"),
                     bg="#16213e", fg="#ffffff").pack()
            tk.Label(frame,
                     text=f"من {subject['max']} | النجاح {subject['pass']}",
                     font=("Arial", 8),
                     bg="#16213e", fg="#aaaaaa").pack()

            entry = tk.Entry(frame,
                             font=("Arial", 14, "bold"),
                             width=5, justify="center",
                             bg="#0a0a23", fg="#00ff88",
                             insertbackground="#00ff88",
                             relief="flat", bd=2)
            entry.pack(pady=3, ipady=3)
            self.grade_entries[subject['name']] = entry

        # ??? الأزرار ???
        btn_frame = tk.Frame(input_frame, bg="#16213e")
        btn_frame.pack(fill=tk.X, pady=10)

        buttons_config = [
            ("?  إضافة طالب",        self.add_student,    "#16813d"),
            ("??  تعديل",             self.edit_student,   "#533483"),
            ("???  حذف",              self.delete_student, "#e94560"),
            ("??  طباعة نتيجة طالب",  self.print_student,  "#0f3460"),
            ("??  طباعة الكل",        self.print_all,      "#8b5e00"),
            ("??  حفظ البيانات",      self.save_data,      "#006666"),
            ("??  مسح الحقول",        self.clear_fields,   "#555555"),
        ]

        for text, cmd, color in buttons_config:
            tk.Button(btn_frame, text=text,
                      font=("Arial", 11, "bold"),
                      bg=color, fg="white",
                      activebackground="#ffffff",
                      command=cmd,
                      padx=12, pady=6,
                      cursor="hand2",
                      relief="flat",
                      bd=0).pack(side=tk.RIGHT, padx=3)

        # ???????????? البحث ????????????
        search_frame = tk.Frame(main_frame, bg="#1a1a2e")
        search_frame.pack(fill=tk.X, pady=5)

        tk.Label(search_frame, text="??  بحث :",
                 font=("Arial", 12, "bold"),
                 bg="#1a1a2e", fg="#ffffff").pack(side=tk.RIGHT, padx=5)

        self.search_entry = tk.Entry(search_frame,
                                     font=("Arial", 13),
                                     width=30, justify="right",
                                     bg="#0f3460", fg="white",
                                     insertbackground="white",
                                     relief="flat")
        self.search_entry.pack(side=tk.RIGHT, padx=5, ipady=3)
        self.search_entry.bind("<KeyRelease>", self.search_student)

        # عدد الطلاب
        self.count_label = tk.Label(search_frame,
                                    text="عدد الطلاب: 0",
                                    font=("Arial", 12, "bold"),
                                    bg="#1a1a2e", fg="#00d2ff")
        self.count_label.pack(side=tk.LEFT, padx=20)

        # ???????????? جدول البيانات ????????????
        table_frame = tk.LabelFrame(
            main_frame,
            text="  ??  جدول الدرجات  ",
            font=("Arial", 14, "bold"),
            bg="#16213e", fg="#00d2ff",
            labelanchor="ne"
        )
        table_frame.pack(fill=tk.BOTH, expand=True, pady=5)

        columns = ("num", "name", "arabic", "english",
                   "studies", "science", "math", "religion",
                   "total", "percentage", "grade")

        self.tree = ttk.Treeview(table_frame,
                                 columns=columns,
                                 show="headings",
                                 height=12)

        headers = {
            "num":        ("م",                40),
            "name":       ("اسم الطالب",       200),
            "arabic":     ("العربية\n(100)",   90),
            "english":    ("الانجليزية\n(100)", 90),
            "studies":    ("الدراسات\n(100)",   90),
            "science":    ("العلوم\n(80)",      80),
            "math":       ("الرياضيات\n(100)",  90),
            "religion":   ("الدين\n(50)",       75),
            "total":      ("المجموع\n(530)",    90),
            "percentage": ("النسبة %",          80),
            "grade":      ("التقدير",           90),
        }

        for col, (text, width) in headers.items():
            self.tree.heading(col, text=text)
            anchor = "e" if col == "name" else "center"
            self.tree.column(col, width=width, anchor=anchor, minwidth=50)

        # أشرطة التمرير
        v_scroll = ttk.Scrollbar(table_frame, orient=tk.VERTICAL,
                                 command=self.tree.yview)
        h_scroll = ttk.Scrollbar(table_frame, orient=tk.HORIZONTAL,
                                 command=self.tree.xview)
        self.tree.configure(yscrollcommand=v_scroll.set,
                            xscrollcommand=h_scroll.set)

        v_scroll.pack(side=tk.LEFT, fill=tk.Y)
        h_scroll.pack(side=tk.BOTTOM, fill=tk.X)
        self.tree.pack(fill=tk.BOTH, expand=True, padx=5, pady=5)

        self.tree.bind("<<TreeviewSelect>>", self.on_select)

        # ??? ألوان الصفوف ???
        self.tree.tag_configure("fail",
                                foreground="#ff4444",
                                background="#2d1f1f",
                                font=("Arial", 11, "bold"))
        self.tree.tag_configure("excellent",
                                foreground="#44ff44",
                                background="#1f2d1f")
        self.tree.tag_configure("vgood",
                                foreground="#88ccff",
                                background="#1a2a3a")
        self.tree.tag_configure("good",
                                foreground="#ffcc44",
                                background="#2d2a1f")
        self.tree.tag_configure("pass",
                                foreground="#ffffff",
                                background="#0f3460")

    # ??????????????????????????????????????????
    #              حساب التقدير
    # ??????????????????????????????????????????
    def calculate_grade(self, total):
        pct = (total / self.total_max) * 100
        if pct >= 85:
            return "ممتاز"
        elif pct >= 75:
            return "جيد جداً"
        elif pct >= 65:
            return "جيد"
        elif pct >= 50:
            return "مقبول"
        else:
            return "ضعيف"

    def check_pass(self, grades):
        for i, subj in enumerate(self.subjects):
            if grades[i] < subj["pass"]:
                return False
        return True

    def get_failed_subjects(self, grades):
        failed = []
        for i, subj in enumerate(self.subjects):
            if grades[i] < subj["pass"]:
                failed.append(subj["name"])
        return failed

    # ??????????????????????????????????????????
    #          التحقق من صحة البيانات
    # ??????????????????????????????????????????
    def validate_grades(self):
        grades = []
        for subj in self.subjects:
            val = self.grade_entries[subj['name']].get().strip()
            if not val:
                messagebox.showerror("خطأ",
                    f"يرجى إدخال درجة {subj['name']}")
                self.grade_entries[subj['name']].focus()
                return None
            try:
                grade = float(val)
            except ValueError:
                messagebox.showerror("خطأ",
                    f"درجة {subj['name']} يجب أن تكون رقماً")
                self.grade_entries[subj['name']].focus()
                return None

            if grade < 0 or grade > subj['max']:
                messagebox.showerror("خطأ",
                    f"درجة {subj['name']} يجب أن تكون بين 0 و {subj['max']}")
                self.grade_entries[subj['name']].focus()
                return None
            grades.append(grade)
        return grades

    # ??????????????????????????????????????????
    #               العمليات
    # ??????????????????????????????????????????
    def add_student(self):
        name = self.name_entry.get().strip()
        if not name:
            messagebox.showerror("خطأ", "يرجى إدخال اسم الطالب")
            self.name_entry.focus()
            return

        grades = self.validate_grades()
        if grades is None:
            return

        total = sum(grades)
        passed = self.check_pass(grades)
        grade_text = self.calculate_grade(total) if passed else "راسب"

        student = {
            "name":   name,
            "grades": grades,
            "total":  total,
            "grade":  grade_text
        }

        self.students.append(student)
        self.refresh_table()
        self.clear_fields()
        self.save_data()
        messagebox.showinfo("? نجاح", f"تم إضافة الطالب «{name}» بنجاح")

    def edit_student(self):
        sel = self.tree.selection()
        if not sel:
            messagebox.showerror("خطأ", "يرجى اختيار طالب من الجدول أولاً")
            return

        idx = self.tree.index(sel[0])
        name = self.name_entry.get().strip()
        if not name:
            messagebox.showerror("خطأ", "يرجى إدخال اسم الطالب")
            return

        grades = self.validate_grades()
        if grades is None:
            return

        total = sum(grades)
        passed = self.check_pass(grades)
        grade_text = self.calculate_grade(total) if passed else "راسب"

        self.students[idx] = {
            "name": name, "grades": grades,
            "total": total, "grade": grade_text
        }
        self.refresh_table()
        self.clear_fields()
        self.save_data()
        messagebox.showinfo("? نجاح", "تم تعديل بيانات الطالب بنجاح")

    def delete_student(self):
        sel = self.tree.selection()
        if not sel:
            messagebox.showerror("خطأ", "يرجى اختيار طالب من الجدول أولاً")
            return

        if messagebox.askyesno("?? تأكيد الحذف",
                               "هل أنت متأكد من حذف هذا الطالب؟"):
            idx = self.tree.index(sel[0])
            name = self.students[idx]["name"]
            del self.students[idx]
            self.refresh_table()
            self.clear_fields()
            self.save_data()
            messagebox.showinfo("? تم", f"تم حذف الطالب «{name}»")

    def on_select(self, event):
        sel = self.tree.selection()
        if not sel:
            return
        idx = self.tree.index(sel[0])
        st = self.students[idx]

        self.name_entry.delete(0, tk.END)
        self.name_entry.insert(0, st["name"])

        for i, subj in enumerate(self.subjects):
            e = self.grade_entries[subj['name']]
            e.delete(0, tk.END)
            val = st["grades"][i]
            e.insert(0, str(int(val)) if val == int(val) else str(val))

    def clear_fields(self):
        self.name_entry.delete(0, tk.END)
        for subj in self.subjects:
            self.grade_entries[subj['name']].delete(0, tk.END)
        self.name_entry.focus()

    # ??????????????????????????????????????????
    #             تحديث الجدول
    # ??????????????????????????????????????????
    def refresh_table(self, students=None):
        for item in self.tree.get_children():
            self.tree.delete(item)

        display = students if students is not None else self.students

        for i, st in enumerate(display):
            pct = (st["total"] / self.total_max) * 100
            values = (
                i + 1,
                st["name"],
                int(st["grades"][0]),
                int(st["grades"][1]),
                int(st["grades"][2]),
                int(st["grades"][3]),
                int(st["grades"][4]),
                int(st["grades"][5]),
                int(st["total"]),
                f"{pct:.1f}%",
                st["grade"]
            )

            if st["grade"] == "راسب":
                tag = "fail"
            elif st["grade"] == "ممتاز":
                tag = "excellent"
            elif st["grade"] == "جيد جداً":
                tag = "vgood"
            elif st["grade"] == "جيد":
                tag = "good"
            else:
                tag = "pass"

            self.tree.insert("", tk.END, values=values, tags=(tag,))

        count = len(display)
        self.count_label.config(text=f"عدد الطلاب: {count}")

    def search_student(self, event=None):
        q = self.search_entry.get().strip()
        if not q:
            self.refresh_table()
            return
        filtered = [s for s in self.students if q in s["name"]]
        self.refresh_table(filtered)

    # ??????????????????????????????????????????
    #        طباعة نتيجة طالب واحد
    # ??????????????????????????????????????????
    def print_student(self):
        sel = self.tree.selection()
        if not sel:
            messagebox.showerror("خطأ", "يرجى اختيار طالب من الجدول أولاً")
            return
        idx = self.tree.index(sel[0])
        self.show_student_report(self.students[idx])

    def show_student_report(self, student):
        win = tk.Toplevel(self.root)
        win.title(f"نتيجة الطالب - {student['name']}")
        win.geometry("650x820")
        win.configure(bg="white")
        win.resizable(False, False)

        canvas = tk.Canvas(win, bg="white", width=630, height=740)
        canvas.pack(fill=tk.BOTH, expand=True, padx=10, pady=10)

        # ??? رأس التقرير ???
        canvas.create_text(315, 25, text="جمهورية مصر العربية",
                           font=("Arial", 13, "bold"), fill="#333")
        canvas.create_text(315, 48, text="وزارة التربية والتعليم",
                           font=("Arial", 12), fill="#333")
        canvas.create_text(315, 75, text="إدارة غرب الزقازيق التعليمية",
                           font=("Arial", 18, "bold"), fill="#e94560")
        canvas.create_line(40, 95, 590, 95, fill="#e94560", width=3)
        canvas.create_text(315, 120, text="?  بيان درجات طالب  ?",
                           font=("Arial", 18, "bold"), fill="#0f3460")
        canvas.create_line(40, 140, 590, 140, fill="#e94560", width=3)

        # الاسم والتاريخ
        canvas.create_text(590, 165, text=f"اسم الطالب :  {student['name']}",
                           font=("Arial", 14, "bold"), fill="#333", anchor="e")
        canvas.create_text(40, 165,
                           text=f"التاريخ :  {datetime.now().strftime('%Y / %m / %d')}",
                           font=("Arial", 11), fill="#888", anchor="w")

        # ??? جدول الدرجات ???
        y = 200
        rh = 48
        cols = [40, 150, 270, 400, 590]  # x-positions

        # رأس الجدول
        canvas.create_rectangle(cols[0], y, cols[-1], y + rh,
                                fill="#0f3460", outline="#333", width=2)
        canvas.create_text(95, y+24, text="الحالة",
                           font=("Arial", 12, "bold"), fill="white")
        canvas.create_text(210, y+24, text="الدرجة",
                           font=("Arial", 12, "bold"), fill="white")
        canvas.create_text(335, y+24, text="الدرجة العظمى",
                           font=("Arial", 12, "bold"), fill="white")
        canvas.create_text(495, y+24, text="المادة",
                           font=("Arial", 12, "bold"), fill="white")
        y += rh

        for i, subj in enumerate(self.subjects):
            grade = student["grades"][i]
            fail = grade < subj["pass"]
            bg = "#ffe0e0" if fail else "#e8ffe8"
            fg = "#cc0000" if fail else "#006600"
            status = "راسب  ?" if fail else "ناجح  ?"

            canvas.create_rectangle(cols[0], y, cols[-1], y + rh,
                                    fill=bg, outline="#999")
            canvas.create_text(95,  y+24, text=status,
                               font=("Arial", 12, "bold"), fill=fg)
            canvas.create_text(210, y+24, text=str(int(grade)),
                               font=("Arial", 16, "bold"), fill=fg)
            canvas.create_text(335, y+24, text=str(subj["max"]),
                               font=("Arial", 12), fill="#333")
            canvas.create_text(495, y+24, text=subj["name"],
                               font=("Arial", 12, "bold"), fill="#333")
            y += rh

        # المجموع
        canvas.create_rectangle(cols[0], y, cols[-1], y + rh,
                                fill="#0f3460", outline="#333", width=2)
        canvas.create_text(315, y+24,
                           text=f"المجموع الكلي :  {int(student['total'])}  من  {self.total_max}",
                           font=("Arial", 15, "bold"), fill="white")
        y += rh

        # النسبة المئوية
        pct = (student['total'] / self.total_max) * 100
        canvas.create_rectangle(cols[0], y, cols[-1], y + rh,
                                fill="#f0f0f0", outline="#999")
        canvas.create_text(315, y+24,
                           text=f"النسبة المئوية :  {pct:.1f} %",
                           font=("Arial", 14, "bold"), fill="#333")
        y += rh

        # التقدير
        gc = "#cc0000" if student["grade"] == "راسب" else "#006600"
        canvas.create_rectangle(cols[0], y, cols[-1], y + rh + 5,
                                fill="#f8f8f8", outline="#333", width=2)
        canvas.create_text(315, y+26,
                           text=f"التقدير  :  {student['grade']}",
                           font=("Arial", 20, "bold"), fill=gc)

        # ??? أزرار ???
        btn_row = tk.Frame(win, bg="white")
        btn_row.pack(pady=8)

        tk.Button(btn_row, text="???  طباعة / حفظ PDF",
                  font=("Arial", 13, "bold"),
                  bg="#16813d", fg="white",
                  command=lambda: self.save_report_html(student),
                  padx=20, pady=6, cursor="hand2",
                  relief="flat").pack(side=tk.RIGHT, padx=5)

        tk.Button(btn_row, text="?  إغلاق",
                  font=("Arial", 13, "bold"),
                  bg="#e94560", fg="white",
                  command=win.destroy,
                  padx=20, pady=6, cursor="hand2",
                  relief="flat").pack(side=tk.RIGHT, padx=5)

    # ??????????????????????????????????????????
    #     حفظ تقرير طالب واحد كـ HTML للطباعة
    # ??????????????????????????????????????????
    def save_report_html(self, student):
        filename = filedialog.asksaveasfilename(
            defaultextension=".html",
            filetypes=[("HTML files", "*.html")],
            initialname=f"نتيجة_{student['name']}.html"
        )
        if not filename:
            return

        pct = (student['total'] / self.total_max) * 100
        grade_color = "#cc0000" if student["grade"] == "راسب" else "#006600"

        rows_html = ""
        for i, subj in enumerate(self.subjects):
            g = student["grades"][i]
            fail = g < subj["pass"]
            cls = "fail" if fail else "pass-cell"
            status = "راسب  ?" if fail else "ناجح  ?"
            rows_html += f"""
            <tr class="{cls}">
                <td>{status}</td>
                <td style="font-size:18px;font-weight:bold">{int(g)}</td>
                <td>{subj['max']}</td>
                <td style="font-weight:bold">{subj['name']}</td>
            </tr>"""

        html = f"""<!DOCTYPE html>
<html dir="rtl" lang="ar">
<head>
<meta charset="UTF-8">
<title>نتيجة الطالب - {student['name']}</title>
<style>
  @page {{ size: A4; margin: 15mm; }}
  * {{ box-sizing: border-box; }}
  body {{ font-family: 'Arial', 'Tahoma', sans-serif;
          background: #f5f5f5; direction: rtl; margin: 0; padding: 20px; }}
  .page {{ max-width: 700px; margin: 0 auto; background: white;
           padding: 35px; border: 3px solid #0f3460;
           box-shadow: 0 4px 15px rgba(0,0,0,0.2); }}
  .header {{ text-align: center; border-bottom: 3px solid #e94560;
             padding-bottom: 15px; margin-bottom: 15px; }}
  .header h1 {{ color: #e94560; margin: 5px 0; font-size: 24px; }}
  .header h2 {{ color: #0f3460; margin: 5px 0; font-size: 20px; }}
  .header h3 {{ color: #333; margin: 3px 0; font-size: 15px; }}
  .info {{ display: flex; justify-content: space-between;
           margin: 15px 0; font-size: 16px; }}
  .info .name {{ font-weight: bold; font-size: 18px; }}
  table {{ width: 100%; border-collapse: collapse; margin: 20px 0; }}
  th {{ background: #0f3460; color: white; padding: 14px 10px;
       border: 2px solid #333; font-size: 14px; }}
  td {{ padding: 12px 10px; border: 2px solid #ccc;
       text-align: center; font-size: 15px; }}
  .pass-cell {{ background: #e8ffe8; color: #006600; }}
  .fail {{ background: #ffe0e0; color: #cc0000; font-weight: bold; }}
  .total-row {{ background: #0f3460; color: white;
                font-weight: bold; font-size: 18px; }}
  .total-row td {{ border-color: #333; padding: 16px; }}
  .result-row td {{ font-size: 16px; padding: 14px;
                    border: 2px solid #ccc; }}
  .grade-val {{ font-size: 24px; font-weight: bold; }}
  .footer {{ text-align: center; margin-top: 30px;
             padding-top: 15px; border-top: 2px solid #e94560;
             color: #888; font-size: 13px; }}
  .no-print {{ text-align: center; margin: 20px; }}
  .no-print button {{ padding: 12px 40px; font-size: 18px;
                      background: #16813d; color: white;
                      border: none; cursor: pointer;
                      border-radius: 8px; margin: 5px; }}
  .no-print button:hover {{ opacity: 0.85; }}
  .no-print .close-btn {{ background: #e94560; }}
  @media print {{
    body {{ background: white; padding: 0; }}
    .page {{ box-shadow: none; border: none; }}
    .no-print {{ display: none; }}
  }}
</style>
</head>
<body>
<div class="page">
  <div class="header">
    <h3>جمهورية مصر العربية</h3>
    <h3>وزارة التربية والتعليم</h3>
    <h1>?? إدارة غرب الزقازيق التعليمية</h1>
    <h2>? بيان درجات طالب ?</h2>
  </div>

  <div class="info">
    <span class="name">?? اسم الطالب :  {student['name']}</span>
    <span>?? التاريخ :  {datetime.now().strftime('%Y / %m / %d')}</span>
  </div>

  <table>
    <thead>
      <tr>
        <th style="width:20%">الحالة</th>
        <th style="width:20%">الدرجة</th>
        <th style="width:25%">الدرجة العظمى</th>
        <th style="width:35%">المادة</th>
      </tr>
    </thead>
    <tbody>
      {rows_html}
      <tr class="total-row">
        <td colspan="4">المجموع الكلي :  {int(student['total'])}  من  {self.total_max}</td>
      </tr>
      <tr class="result-row">
        <td colspan="2">النسبة المئوية :  {pct:.1f} %</td>
        <td colspan="2">
          <span class="grade-val" style="color:{grade_color}">
            التقدير :  {student['grade']}
          </span>
        </td>
      </tr>
    </tbody>
  </table>

  <div class="footer">
    <p>إدارة غرب الزقازيق التعليمية</p>
    <p>مع أطيب التمنيات بالنجاح والتوفيق ??</p>
  </div>
</div>

<div class="no-print">
  <button onclick="window.print()">??? طباعة</button>
  <button class="close-btn" onclick="window.close()">? إغلاق</button>
</div>
</body>
</html>"""

        with open(filename, 'w', encoding='utf-8') as f:
            f.write(html)

        os.startfile(filename)
        messagebox.showinfo("? نجاح",
            "تم حفظ التقرير — سيفتح في المتصفح للطباعة\n"
            "اضغط Ctrl+P في المتصفح للطباعة أو الحفظ كـ PDF")

    # ??????????????????????????????????????????
    #          طباعة جميع الطلاب
    # ??????????????????????????????????????????
    def print_all(self):
        if not self.students:
            messagebox.showerror("خطأ", "لا يوجد طلاب")
            return

        filename = filedialog.asksaveasfilename(
            defaultextension=".html",
            filetypes=[("HTML files", "*.html")],
            initialname="كشف_درجات_جميع_الطلاب.html"
        )
        if not filename:
            return

        rows = ""
        for i, st in enumerate(self.students):
            pct = (st["total"] / self.total_max) * 100
            row_class = "fail-row" if st["grade"] == "راسب" else ""

            cells = ""
            for j in range(6):
                fail = st["grades"][j] < self.subjects[j]["pass"]
                cls = ' class="fail-cell"' if fail else ""
                cells += f'<td{cls}>{int(st["grades"][j])}</td>\n'

            grade_cls = ' class="fail-cell"' if st["grade"] == "راسب" else ""

            rows += f"""
            <tr class="{row_class}">
              <td>{i+1}</td>
              <td class="name-cell">{st['name']}</td>
              {cells}
              <td style="font-weight:bold">{int(st['total'])}</td>
              <td>{pct:.1f}%</td>
              <td{grade_cls} style="font-weight:bold">{st['grade']}</td>
            </tr>"""

        # إحصائيات
        total_students = len(self.students)
        passed_count = sum(1 for s in self.students if s["grade"] != "راسب")
        failed_count = total_students - passed_count
        pass_rate = (passed_count / total_students * 100) if total_students else 0

        html = f"""<!DOCTYPE html>
<html dir="rtl" lang="ar">
<head>
<meta charset="UTF-8">
<title>كشف درجات جميع الطلاب</title>
<style>
  @page {{ size: A4 landscape; margin: 10mm; }}
  body {{ font-family: Arial, Tahoma; background: #f5f5f5;
          direction: rtl; margin: 0; padding: 15px; }}
  .container {{ max-width: 1100px; margin: 0 auto;
                background: white; padding: 25px;
                border: 2px solid #0f3460; }}
  .header {{ text-align: center; margin-bottom: 20px; }}
  .header h1 {{ color: #e94560; margin: 5px; }}
  .header h2 {{ color: #0f3460; margin: 5px; }}
  table {{ width: 100%; border-collapse: collapse; margin: 15px 0;
           font-size: 13px; }}
  th {{ background: #0f3460; color: white; padding: 10px 6px;
       border: 2px solid #333; }}
  td {{ padding: 8px 6px; border: 1px solid #999;
       text-align: center; }}
  .name-cell {{ text-align: right; padding-right: 10px;
                font-weight: bold; }}
  .fail-cell {{ color: #cc0000; background: #ffe0e0;
                font-weight: bold; }}
  .fail-row {{ background: #fff5f5; }}
  tr:nth-child(even):not(.fail-row) {{ background: #f8f8ff; }}
  .stats {{ display: flex; justify-content: space-around;
            margin: 20px 0; padding: 15px;
            background: #f0f0ff; border-radius: 10px; }}
  .stat-box {{ text-align: center; padding: 10px 25px; }}
  .stat-box .num {{ font-size: 28px; font-weight: bold;
                    color: #0f3460; }}
  .stat-box .label {{ font-size: 14px; color: #666; }}
  .no-print {{ text-align: center; margin: 20px; }}
  .no-print button {{ padding: 12px 35px; font-size: 16px;
                      background: #16813d; color: white;
                      border: none; cursor: pointer;
                      border-radius: 8px; margin: 5px; }}
  @media print {{
    body {{ background: white; padding: 0; }}
    .container {{ border: none; }}
    .no-print {{ display: none; }}
  }}
</style>
</head>
<body>
<div class="container">
  <div class="header">
    <h3>جمهورية مصر العربية — وزارة التربية والتعليم</h3>
    <h1>?? إدارة غرب الزقازيق التعليمية</h1>
    <h2>كشف درجات الطلاب</h2>
    <p>?? التاريخ :  {datetime.now().strftime('%Y / %m / %d')}</p>
  </div>

  <table>
    <thead>
      <tr>
        <th>م</th>
        <th>اسم الطالب</th>
        <th>العربية<br>(100)</th>
        <th>الانجليزية<br>(100)</th>
        <th>الدراسات<br>(100)</th>
        <th>العلوم<br>(80)</th>
        <th>الرياضيات<br>(100)</th>
        <th>الدين<br>(50)</th>
        <th>المجموع<br>(530)</th>
        <th>النسبة %</th>
        <th>التقدير</th>
      </tr>
    </thead>
    <tbody>
      {rows}
    </tbody>
  </table>

  <div class="stats">
    <div class="stat-box">
      <div class="num">{total_students}</div>
      <div class="label">إجمالي الطلاب</div>
    </div>
    <div class="stat-box">
      <div class="num" style="color:#006600">{passed_count}</div>
      <div class="label">ناجح</div>
    </div>
    <div class="stat-box">
      <div class="num" style="color:#cc0000">{failed_count}</div>
      <div class="label">راسب</div>
    </div>
    <div class="stat-box">
      <div class="num" style="color:#0f3460">{pass_rate:.1f}%</div>
      <div class="label">نسبة النجاح</div>
    </div>
  </div>
</div>

<div class="no-print">
  <button onclick="window.print()">??? طباعة</button>
</div>
</body>
</html>"""

        with open(filename, 'w', encoding='utf-8') as f:
            f.write(html)

        os.startfile(filename)

    # ??????????????????????????????????????????
    #          حفظ وتحميل البيانات
    # ??????????????????????????????????????????
    def save_data(self):
        try:
            with open("students_data.json", 'w', encoding='utf-8') as f:
                json.dump(self.students, f, ensure_ascii=False, indent=2)
        except Exception as e:
            messagebox.showerror("خطأ", f"خطأ في حفظ البيانات:\n{e}")

    def load_data(self):
        try:
            if os.path.exists("students_data.json"):
                with open("students_data.json", 'r', encoding='utf-8') as f:
                    self.students = json.load(f)
                self.refresh_table()
        except Exception as e:
            messagebox.showerror("خطأ", f"خطأ في تحميل البيانات:\n{e}")


# ??????????????????????????????????????????????
#                تشغيل البرنامج
# ??????????????????????????????????????????????
if __name__ == "__main__":
    root = tk.Tk()
    app = StudentGradesSystem(root)
    root.mainloop()
