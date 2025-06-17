# Step 1: Import libraries
from google.colab import files
uploaded = files.upload()
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import folium
from folium.plugins import HeatMap
import zipfile
import io

# Step 2: Load the dataset
# Check if the uploaded file is a zip file and extract it
for fn in uploaded.keys():
    if fn.endswith('.zip'):
        with zipfile.ZipFile(io.BytesIO(uploaded[fn]), 'r') as z:
            z.extractall()
        # Assuming the csv file inside the zip has the same name without .zip
        csv_filename = fn.replace('.zip', '')
    else:
        csv_filename = fn

df = pd.read_csv(csv_filename)  # Use the extracted or uploaded csv filename


# Step 3: Check available columns
print("Available columns:")
print(df.columns.tolist())

# Step 4: Parse date and time (adjust according to your dataset)
# Let's assume columns are 'Date' and 'Time' or a combined 'Timestamp'
if 'Start_Time' in df.columns:
    df['Datetime'] = pd.to_datetime(df['Start_Time'], errors='coerce')
elif 'Timestamp' in df.columns:
    df['Datetime'] = pd.to_datetime(df['Timestamp'], errors='coerce')
else:
    print("Date/Time columns not found!")
    df['Datetime'] = pd.NaT  # Fallback

# Step 5: Extract time features
df['Hour'] = df['Datetime'].dt.hour
df['Weekday'] = df['Datetime'].dt.day_name()

# Step 6: Accidents by Hour
plt.figure(figsize=(10,6))
sns.countplot(x='Hour', data=df, palette='viridis')
plt.title("Accidents by Hour of Day")
plt.xlabel("Hour")
plt.ylabel("Number of Accidents")
plt.tight_layout()
plt.show()

# Step 7: Weather Conditions (adjust column name)
if 'Weather_Condition' in df.columns:
    plt.figure(figsize=(12,6))
    top_weather = df['Weather_Condition'].value_counts().nlargest(10)
    sns.barplot(x=top_weather.index, y=top_weather.values, palette='coolwarm')
    plt.title("Top 10 Weather Conditions During Accidents")
    plt.xlabel("Weather Condition")
    plt.ylabel("Number of Accidents")
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()
else:
    print("Column 'Weather_Condition' not found.")

# Step 8: Road Condition Analysis (adjust column name)
if 'Road_Condition' in df.columns:
    plt.figure(figsize=(10,6))
    sns.countplot(data=df, x='Road_Condition', order=df['Road_Condition'].value_counts().index[:5], palette='Set2')
    plt.title("Top Road Conditions During Accidents")
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()
else:
    print("Column 'Road_Condition' not found.")

# Step 9: Weekday Analysis
plt.figure(figsize=(10,6))
sns.countplot(x='Weekday', data=df, order=['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'])
plt.title("Accidents by Day of the Week")
plt.xlabel("Day")
plt.ylabel("Number of Accidents")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

# Step 10: Heatmap of Hotspots (if coordinates exist)
if 'Start_Lat' in df.columns and 'Start_Lng' in df.columns:
    sample_df = df[['Start_Lat', 'Start_Lng']].dropna().sample(n=1000, random_state=1)
    map_accidents = folium.Map(location=[sample_df['Start_Lat'].mean(), sample_df['Start_Lng'].mean()], zoom_start=10)
    HeatMap(data=sample_df[['Start_Lat', 'Start_Lng']]).add_to(map_accidents)
    map_accidents.save("accident_hotspots.html")
    print("Hotspot map saved as 'accident_hotspots.html'")
else:
    print("Latitude/Longitude columns not found.")
