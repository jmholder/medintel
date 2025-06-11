import streamlit as st
from PIL import Image
import pytesseract
import openai
import tempfile
import fitz  # PyMuPDF

st.set_page_config(page_title="Clinical Document Analyzer", layout="centered")
st.title("📄 Clinical Document Analyzer")
st.markdown("Upload a doctor’s note or prescription to receive a clear explanation.\n\n⚠️ **This is for informational purposes only and not medical advice.**")

uploaded_file = st.file_uploader("Upload file", type=["png", "jpg", "jpeg", "pdf"])

if uploaded_file:
    with tempfile.NamedTemporaryFile(delete=False) as tmp_file:
        tmp_file.write(uploaded_file.read())
        temp_path = tmp_file.name

    file_text = ""

    if uploaded_file.name.endswith(".pdf"):
        doc = fitz.open(temp_path)
        for page in doc:
            file_text += page.get_text()
        if not file_text.strip():
            st.warning("No extractable text found. Try uploading a scanned image instead.")
    else:
        image = Image.open(temp_path)
        file_text = pytesseract.image_to_string(image)

    st.subheader("📜 Extracted Text")
    st.text(file_text.strip())

    if file_text.strip():
        # Add your OpenAI API key in Streamlit secrets or here directly
        openai.api_key = st.secrets["OPENAI_API_KEY"]  # Or replace with: "sk-..."
        with st.spinner("Analyzing with AI..."):
            response = openai.ChatCompletion.create(
                model="gpt-4",
                messages=[
                    {"role": "system", "content": "You are a medical assistant. Your job is to clearly and kindly explain clinical documents to patients."},
                    {"role": "user", "content": file_text}
                ]
            )
            analysis = response['choices'][0]['message']['content']
        st.subheader("🧠 AI Explanation")
        st.markdown(analysis)
        st.info("This tool provides general information and does not replace professional medical advice.")
