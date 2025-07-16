import re
import tkinter as tk
from tkinter import ttk


common_passwords = [
    '123456', 'password', '12345678', 'qwerty', 'abc123', 'password1', '111111'
]

def check_password_strength(password):
    score = 0
    feedback = []

    if len(password) >= 8:
        score += 1
    else:
        feedback.append("❌ At least 8 characters.")

    if re.search(r'[A-Z]', password):
        score += 1
    else:
        feedback.append("❌ Add an uppercase letter.")

    if re.search(r'[a-z]', password):
        score += 1
    else:
        feedback.append("❌ Add a lowercase letter.")

    if re.search(r'\d', password):
        score += 1
    else:
        feedback.append("❌ Add a digit.")

    if re.search(r'[\W_]', password):
        score += 1
    else:
        feedback.append("❌ Add a special character (!@#$...)")

    if password.lower() in [p.lower() for p in common_passwords]:
        feedback.append("❌ Password is too common.")
        score = 0

    return score, feedback

def update_strength(event=None):
    password = password_entry.get()
    score, feedback = check_password_strength(password)

    progress_bar['value'] = score * 20
    if score == 5:
        result_var.set("✅ Strong 💪")
        result_label.config(foreground="green")
    elif score >= 3:
        result_var.set("⚠ Medium")
        result_label.config(foreground="orange")
    else:
        result_var.set("❌ Weak")
        result_label.config(foreground="red")

    # Show suggestions
    feedback_box.config(state='normal')
    feedback_box.delete("1.0", tk.END)
    for f in feedback:
        feedback_box.insert(tk.END, f + "\n")
    feedback_box.config(state='disabled')


root = tk.Tk()
root.title("Advanced Password Strength Checker")
root.geometry("450x320")
root.resizable(False, False)

style = ttk.Style(root)
style.theme_use("clam")
style.configure("TProgressbar", thickness=20, troughcolor='#e0e0e0', background='#4caf50')


tk.Label(root, text="🔐 Password Strength Checker", font=("Helvetica", 16, "bold")).pack(pady=10)


tk.Label(root, text="Enter your password:", font=("Helvetica", 11)).pack()
password_entry = tk.Entry(root, show="*", font=("Helvetica", 12), width=30)
password_entry.pack(pady=5)
password_entry.bind("<KeyRelease>", update_strength)


progress_bar = ttk.Progressbar(root, length=300, maximum=100, value=0)
progress_bar.pack(pady=10)


result_var = tk.StringVar()
result_label = tk.Label(root, textvariable=result_var, font=("Helvetica", 12, "bold"))
result_label.pack()


tk.Label(root, text="Suggestions:", font=("Helvetica", 11)).pack(pady=5)
feedback_box = tk.Text(root, height=5, width=50, font=("Helvetica", 10), bg="#f9f9f9", wrap='word', bd=1, relief='sunken')
feedback_box.pack(pady=5)
feedback_box.config(state='disabled')


root.mainloop()
