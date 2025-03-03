# Define project structure
$projectName = "QR_Web"
$folders = @("$projectName", "$projectName\templates")

# Create project directories
foreach ($folder in $folders) {
    New-Item -ItemType Directory -Path $folder -Force
}

# Create app.py file
$appPyContent = @'
from flask import Flask, request, render_template
import qrcode
import io
from flask import send_file

app = Flask(__name__)

@app.route("/")
def home():
    return render_template("index.html")

@app.route("/generate", methods=["POST"])
def generate_qr():
    url = request.form["url"]
    qr = qrcode.QRCode(
        version=1,
        error_correction=qrcode.constants.ERROR_CORRECT_L,
        box_size=10,
        border=4,
    )
    qr.add_data(url)
    qr.make(fit=True)

    img = qr.make_image(fill="black", back_color="white")
    buf = io.BytesIO()
    img.save(buf)
    buf.seek(0)

    return send_file(buf, mimetype="image/png")

if __name__ == "__main__":
    app.run(debug=True)
'@

Set-Content -Path "$projectName\app.py" -Value $appPyContent

# Create index.html file
$indexHtmlContent = @'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>QR Code Generator</title>
</head>
<body>
    <h1>QR Code Generator</h1>
    <form action="/generate" method="post">
        <label for="url">Enter URL:</label>
        <input type="url" id="url" name="url" required>
        <button type="submit">Generate QR Code</button>
    </form>
</body>
</html>
'@

Set-Content -Path "$projectName\templates\index.html" -Value $indexHtmlContent

# Create requirements.txt file
$requirementsContent = @'
Flask
qrcode[pil]
'@

Set-Content -Path "$projectName\requirements.txt" -Value $requirementsContent

# Output instructions
Write-Output "Project '$projectName' created successfully."
Write-Output "To run the project, follow these steps:"
Write-Output "1. Navigate to the project directory: cd $projectName"
Write-Output "2. Create a virtual environment: python -m venv venv"
Write-Output "3. Activate the virtual environment:"
Write-Output "   - For Windows: .\venv\Scripts\Activate"
Write-Output "   - For macOS/Linux: source venv/bin/activate"
Write-Output "4. Install the required packages: pip install -r requirements.txt"
Write-Output "5. Run the Flask app: python app.py"
Write-Output "6. Open a web browser and go to http://127.0.0.1:5000/"