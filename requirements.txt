import streamlit as st
import pandas as pd

# Example cable database (you can replace with Excel later)
cable_db = pd.DataFrame({
    "Size (mm²)": ["1.5", "2.5", "4", "6", "10"],
    "Ampacity (A)": [15, 20, 25, 32, 45]
})

st.title("⚡ Simple Cable Sizing Tool")

# User inputs
load_current = st.slider("Load Current (A)", min_value=5, max_value=60, value=18, step=1)

installation_factor = st.selectbox(
    "Installation Method (Derating Factor)",
    options={
        "Open Air (1.0)": 1.0,
        "Conduit (0.8)": 0.8,
        "Buried (0.7)": 0.7,
    }
)

# Calculation
required_ampacity = load_current / installation_factor

# Select first cable that meets requirement
selected_cable = cable_db[cable_db["Ampacity (A)"] >= required_ampacity].head(1)

# Show results
st.write(f"**Load current:** {load_current} A")
st.write(f"**Derating factor:** {installation_factor}")
st.write(f"**Required ampacity:** {required_ampacity:.1f} A")

if not selected_cable.empty:
    st.success(f"✅ Recommended cable size: {selected_cable.iloc[0]['Size (mm²)']} mm² "
               f"(Ampacity {selected_cable.iloc[0]['Ampacity (A)']} A)")
else:
    st.error("❌ No suitable cable found in database!")

# Show database for reference
with st.expander("📖 Cable Database"):
    st.table(cable_db)
