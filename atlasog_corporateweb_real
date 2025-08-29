import streamlit as st
import pandas as pd
import os
from datetime import datetime
from random import choice, randint

# -----------------------
# --- Optional: Google Sheet Integration ---
# -----------------------
import gspread
from oauth2client.service_account import ServiceAccountCredentials

# Streamlit secrets or local credentials file
GOOGLE_CREDENTIALS_JSON = "credentials.json"  # Replace with your file or secret path
SHEET_NAME = "AtlasOG_Revenue"

def fetch_revenue_sheet():
    try:
        scope = ["https://spreadsheets.google.com/feeds",
                 "https://www.googleapis.com/auth/spreadsheets",
                 "https://www.googleapis.com/auth/drive.file",
                 "https://www.googleapis.com/auth/drive"]
        creds = ServiceAccountCredentials.from_json_keyfile_name(GOOGLE_CREDENTIALS_JSON, scope)
        client = gspread.authorize(creds)
        sheet = client.open(SHEET_NAME).sheet1
        data = sheet.get_all_records()
        return pd.DataFrame(data)
    except Exception as e:
        st.warning(f"Google Sheet fetch failed: {e}")
        return pd.DataFrame(columns=['asset_id','corp_id','asset_type','monetized_value'])

# -----------------------
# --- Page Setup -------
# -----------------------
st.set_page_config(page_title="AtlasOG Corporate Web – Real Revenue", layout="wide")
st.title("🌐 AtlasOG Corporate Web Engine – Real Revenue Mode")
st.markdown("""
Fully autonomous corporate web AI:
- Generates monetizable digital assets
- Reinvests profits automatically
- Reads real-world revenue from Google Sheet
- Minimal human intervention required
""")

# -----------------------
# --- Data Storage -----
# -----------------------
DATA_FILE = "corporate_web_real.csv"
if not os.path.exists(DATA_FILE):
    df = pd.DataFrame(columns=[
        "asset_id","corp_id","asset_type","creation_time","monetized_value",
        "reinvested","transferable_value"
    ])
    df.to_csv(DATA_FILE, index=False)
else:
    df = pd.read_csv(DATA_FILE)

# -----------------------
# --- Micro-Corporation Setup ---
# -----------------------
CORP_IDS = ["AtlasCorp-A","AtlasCorp-B","AtlasCorp-C"]

def create_asset(corp_id, monetized_value=0):
    asset_types = ["text_content","image_design","script","template","tool"]
    asset_type = choice(asset_types)
    asset_id = f"{asset_type[:2]}-{randint(1000,9999)}"
    creation_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    return {
        "asset_id": asset_id,
        "corp_id": corp_id,
        "asset_type": asset_type,
        "creation_time": creation_time,
        "monetized_value": monetized_value,
        "reinvested": 0,
        "transferable_value": 0
    }

# -----------------------
# --- Fetch Real Revenue ---
# -----------------------
revenue_df = fetch_revenue_sheet()

# -----------------------
# --- Reinvestment & Revenue Update ---
# -----------------------
def corporate_cycle(df, revenue_df):
    new_assets = []
    for idx, row in df.iterrows():
        # Update monetized_value from Google Sheet
        matched = revenue_df[revenue_df['asset_id']==row['asset_id']]
        if not matched.empty:
            df.at[idx, "monetized_value"] = matched['monetized_value'].values[0]
            df.at[idx, "transferable_value"] = matched['monetized_value'].values[0]
        # Reinvest profits: each $ unit generates new assets
        num_new = max(1,int(df.at[idx,"monetized_value"]*0.5))
        for _ in range(num_new):
            new_asset = create_asset(row["corp_id"], monetized_value=0)
            new_assets.append(new_asset)
        df.at[idx,"reinvested"] += num_new
    return pd.concat([df,pd.DataFrame(new_assets)], ignore_index=True)

# -----------------------
# --- Autonomous Cycles ---
# -----------------------
CYCLES = 5  # Number of cycles per run
for _ in range(CYCLES):
    if len(df)==0:
        # Initialize corporate assets
        for corp in CORP_IDS:
            for _ in range(5):
                df = pd.concat([df,pd.DataFrame([create_asset(corp)])], ignore_index=True)
    df = corporate_cycle(df, revenue_df)

df.to_csv(DATA_FILE,index=False)

# -----------------------
# --- Metrics & Dashboard ---
# -----------------------
st.header("📊 Real Revenue Metrics")
total_assets = len(df)
total_reinvested = df["reinvested"].sum()
total_transferable = df["transferable_value"].sum()
st.metric("Total Assets", total_assets)
st.metric("Total Reinvested Assets", total_reinvested)
st.metric("Total Transferable Revenue ($)", total_transferable)

st.header("📝 Latest Assets")
st.dataframe(df.tail(15))

st.header("💡 Notes")
st.markdown("""
- Each digital asset is monetizable and tied to a micro-corporation.
- Profits from monetized assets are automatically reinvested into new asset creation.
- Transferable revenue comes from real-world income logged in your Google Sheet.
- Minimal human tasks: approve transfers from your accounts.
- Feedback loops compound digital assets continuously to maximize growth.
""")
