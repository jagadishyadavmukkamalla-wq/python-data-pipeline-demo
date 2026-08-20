import pandas as pd
import numpy as np
from datetime import datetime

def load_data():
    # Ingesting raw dataset (simulating raw landing bucket data)
    raw = {
        "patient_id": [101, 102, 103, 104, 105, None, 107],
        "appointment_date": ["2026-08-01", "2026-08-02", "INVALID_DATE", "2026-08-04", "2026-08-05", "2026-08-06", "2026-08-07"],
        "clinic_location": ["Oakland", "East Bay", "Oakland", "San Francisco", None, "Oakland", "East Bay"],
        "service_units": [12.5, 8.0, -5.0, 15.0, 10.0, 7.5, np.nan],
        "status": ["completed", "completed", "cancelled", "COMPLETED", "pending", "completed", "completed"]
    }
    return pd.DataFrame(raw)

def clean_and_transform(df):
    # Drop rows without primary key
    df = df.dropna(subset=["patient_id"]).copy()
    df["patient_id"] = df["patient_id"].astype(int)

    # Standardize text fields
    df["clinic_location"] = df["clinic_location"].fillna("Unknown").str.strip().str.title()
    df["status"] = df["status"].str.strip().str.lower()

    # Parse dates safely
    df["appointment_date"] = pd.to_datetime(df["appointment_date"], errors="coerce")

    # Clean numerical values (replace negatives and nulls)
    df["service_units"] = pd.to_numeric(df["service_units"], errors="coerce")
    df.loc[df["service_units"] < 0, "service_units"] = 0.0
    df["service_units"] = df["service_units"].fillna(0.0)

    # Add load timestamp
    df["created_at"] = datetime.utcnow()
    
    return df

def build_tables(df):
    # Fact table
    fact_appointments = df[["patient_id", "appointment_date", "service_units", "status", "created_at"]]
    
    # Dimension table
    dim_location = df[["patient_id", "clinic_location"]].drop_duplicates()

    return fact_appointments, dim_location

if __name__ == "__main__":
    print("Running ETL pipeline...")
    
    raw_df = load_data()
    clean_df = clean_and_transform(raw_df)
    fact, dim = build_tables(clean_df)

    print("\nCleaned Fact Appointments:")
    print(fact.head())

    print("\nLocation Dimension:")
    print(dim.head())

    print("\nDone!")
