import streamlit as st
import os
import glob
from pathlib import Path
import pandas as pd
import plotly.express as px
import random

# ---------------------
# ✅ Session state init
# ---------------------
if "page" not in st.session_state:
    st.session_state.page = "Home"

if "folder_path" not in st.session_state:
    st.session_state.folder_path = os.path.expanduser("~/Desktop/Project Jimbo/mock_reports").strip()

# ✅ Store DBs in session state so they persist
if "REPORT_DB" not in st.session_state:
    st.session_state.REPORT_DB = []

if "CORRELATIONS" not in st.session_state:
    st.session_state.CORRELATIONS = []

# ---------------------
# ✅ Sidebar nav — sticky!
# ---------------------
st.sidebar.title("Navigation")

if st.sidebar.button("🏠 Home"):
    st.session_state.page = "Home"
if st.sidebar.button("🗺️ Crime Map"):
    st.session_state.page = "Crime Map"
if st.sidebar.button("🔗 Correlations"):
    st.session_state.page = "Correlations"
if st.sidebar.button("⚙️ Settings"):
    st.session_state.page = "Settings"

page = st.session_state.page

# ---------------------
# ✅ Automated crawling logic (manual for demo)
# ---------------------
folder = Path(st.session_state.folder_path)

if folder.exists() and folder.is_dir():
    files = list(folder.glob("*.docx"))
    for file in files:
        complaint = os.path.splitext(os.path.basename(file))[0]
        if not any(r["complaint_number"] == complaint for r in st.session_state.REPORT_DB):
            st.session_state.REPORT_DB.append({
                "complaint_number": complaint,
                "type": "Theft",
                "date": "2024-06-01",
                "location": "123 Main St",
                "narrative": "Sample narrative for demonstration."
            })

# ---------------------
# ✅ Build fake correlations with score — only if needed
# ---------------------
if st.session_state.REPORT_DB and not st.session_state.CORRELATIONS:
    for i in range(len(st.session_state.REPORT_DB) - 1):
        st.session_state.CORRELATIONS.append({
            "report1": st.session_state.REPORT_DB[i]["complaint_number"],
            "report2": st.session_state.REPORT_DB[i + 1]["complaint_number"],
            "reason": "Similar suspect description and nearby location.",
            "score": random.randint(50, 100)
        })

# ---------------------
# ✅ Filter only strong matches
# ---------------------
high_confidence = [c for c in st.session_state.CORRELATIONS if c["score"] >= 65]

# ---------------------
# ✅ HOME
# ---------------------
if page == "Home":
    st.title("📊 Dashboard")
    st.metric("Reports Crawled", len(st.session_state.REPORT_DB))
    st.metric("Correlations Found", len(high_confidence))

    # Fake chart
    complaint_types = ["Theft", "Burglary", "Fraud", "Vandalism"]
    counts = [5, 8, 2, 3]
    fig = px.bar(x=complaint_types, y=counts, labels={"x": "Complaint Type", "y": "Count"})
    st.plotly_chart(fig)

# ---------------------
# ✅ CRIME MAP
# ---------------------
elif page == "Crime Map":
    st.title("🗺️ Crime Map")
    if not st.session_state.REPORT_DB:
        st.info("No reports found yet.")
    else:
        df = pd.DataFrame({
            "lat": [44.7631 + i * 0.01 for i in range(len(st.session_state.REPORT_DB))],
            "lon": [-85.6206 + i * 0.01 for i in range(len(st.session_state.REPORT_DB))],
            "complaint_number": [r["complaint_number"] for r in st.session_state.REPORT_DB],
        })
        fig = px.scatter_mapbox(
            df,
            lat="lat",
            lon="lon",
            hover_name="complaint_number",
            zoom=8,
            height=500
        )
        fig.update_layout(mapbox_style="open-street-map")
        st.plotly_chart(fig)

# ---------------------
# ✅ CORRELATIONS
# ---------------------
elif page == "Correlations":
    st.title("🔗 High Confidence Correlations")

    if high_confidence:
        for match in high_confidence:
            if st.button(f"{match['report1']} ↔ {match['report2']} ({match['score']}%)"):
                st.write(f"**Why these correlate:** {match['reason']}")
                st.write(f"📄 [View {match['report1']}]()")
                st.write(f"📄 [View {match['report2']}]()")
    else:
        st.info("No strong correlations found yet.")

# ---------------------
# ✅ SETTINGS
# ---------------------
elif page == "Settings":
    st.title("⚙️ Settings")
    new_path = st.text_input("Folder path:", value=st.session_state.folder_path, key="folder_input")
    if st.button("Update Path"):
        st.session_state.folder_path = new_path
        st.success(f"Updated to: {st.session_state.folder_path}")
