import gradio as gr
import traceback
import datetime
import csv
import os

SAVE_PATH = "submissions.csv"

def check_submission(code: str) -> (bool, str):
    try:
        import io
        output = io.StringIO()
        exec_globals = {}
        exec_locals = {}
        import sys
        old_stdout = sys.stdout
        sys.stdout = output
        exec(code, exec_globals, exec_locals)
        sys.stdout = old_stdout
        result = output.getvalue()

        required_phrases = [
            str(7.0), str(3.0), str(10.0), str(2.5), str(25.0), str(1.0),
            "True", "<class 'int'>", "<class 'float'>", "<class 'bool'>"
        ]
        for phrase in required_phrases:
            if phrase not in result:
                return False, f"❌ Missing or incorrect output: {phrase}"
        return True, "✅ All outputs are correct!"
    except Exception as e:
        return False, "❌ Error: " + traceback.format_exc(limit=1)

def save_submission(name, code, status, feedback):
    file_exists = os.path.isfile(SAVE_PATH)
    with open(SAVE_PATH, mode="a", newline='', encoding='utf-8') as f:
        writer = csv.writer(f)
        if not file_exists:
            writer.writerow(["Timestamp", "Name", "Status", "Feedback", "Code"])
        writer.writerow([
            datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
            name,
            status,
            feedback,
            code.replace('\n', '\\n')
        ])

def evaluate(name, code):
    passed, feedback = check_submission(code)
    status = "Pass" if passed else "Fail"
    save_submission(name, code, status, feedback)
    return status, feedback

iface = gr.Interface(
    fn=evaluate,
    inputs=[
        gr.Textbox(label="Your Name"),
        gr.Code(label="Write your code below", language="python", lines=15),
    ],
    outputs=[
        gr.Textbox(label="Status"),
        gr.Textbox(label="Feedback"),
    ],
    title="Assignment 1: Arithmetic & Data Types",
    description="Write Python code that creates variables a = 5, b = 2.0 and performs +, -, *, /, **, %, comparison and prints types.",
)

iface.launch(server_name="0.0.0.0", server_port=8080)
