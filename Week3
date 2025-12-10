########### Question 1 ###########
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

url = 'https://data.cityofnewyork.us/api/views/6fi9-q3ta/rows.csv?accessType=DOWNLOAD'
df = pd.read_csv(url)

df['hour_beginning'] = pd.to_datetime(df['hour_beginning'])

df['day_of_week'] = df['hour_beginning'].dt.dayofweek
df['day_name'] = df['hour_beginning'].dt.day_name()

weekday_df = df[df['day_of_week'] < 5]

daily_counts = weekday_df.groupby('day_name')['Pedestrians'].mean().reset_index()

day_order = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday']
daily_counts['day_name'] = pd.Categorical(daily_counts['day_name'], 
                                          categories=day_order, 
                                          ordered=True)
daily_counts = daily_counts.sort_values('day_name')

# Create the line plot
plt.figure(figsize=(10, 6))
sns.lineplot(data=daily_counts, x='day_name', y='Pedestrians', marker='o')
plt.title('Average Pedestrian Counts by Weekday')
plt.xlabel('Day of Week')
plt.ylabel('Pedestrian Count')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()












######### Question 2 ############

df['hour_beginning'] = pd.to_datetime(df['hour_beginning'])

df_2019 = df[(df['hour_beginning'].dt.year == 2019) & (df['location'] == 'Brooklyn Bridge')]

df_sorted = df_2019.sort_values('weather_summary')

weather_analysis = df_2019.groupby('weather_summary').agg({
    'Pedestrians': ['count', 'mean', 'median', 'std', 'min', 'max']
}).round(2)
print(weather_analysis)

weather_stats = df_2019.groupby('weather_summary').agg({
    'Pedestrians': 'mean',
    'temperature': 'mean',
    'precipitation': 'mean'
}).round(2).sort_values('Pedestrians', ascending=False)
print(weather_stats)

df_corr = df_2019[['Pedestrians', 'temperature', 'precipitation']].dropna()

correlation_matrix = df_corr.corr()
print("\nCorrelation Matrix:")
print(correlation_matrix)

# Visualize correlation matrix
plt.figure(figsize=(8, 6))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', center=0, 
            square=True, linewidths=1, fmt='.3f',
            cbar_kws={"shrink": 0.8})
plt.title('Correlation Matrix: Weather Conditions vs Pedestrian Counts\nBrooklyn Bridge 2019')
plt.tight_layout()
plt.show()

# Additional visualization: Pedestrian counts by weather summary
plt.figure(figsize=(12, 6))
weather_order = df_2019.groupby('weather_summary')['Pedestrians'].mean().sort_values(ascending=False).index
sns.boxplot(data=df_2019, x='weather_summary', y='Pedestrians', order=weather_order)
plt.xticks(rotation=45, ha='right')
plt.title('Pedestrian Counts by Weather Condition\nBrooklyn Bridge 2019')
plt.xlabel('Weather Summary')
plt.ylabel('Pedestrian Count')
plt.tight_layout()
plt.show()

# Scatter plots for numerical weather variables
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# Temperature vs Pedestrians
axes[0].scatter(df_corr['temperature'], df_corr['Pedestrians'], alpha=0.5)
axes[0].set_xlabel('Temperature (°F)')
axes[0].set_ylabel('Pedestrian Count')
axes[0].set_title('Temperature vs Pedestrian Count')
axes[0].grid(True, alpha=0.3)

# Add trend line
z = np.polyfit(df_corr['temperature'], df_corr['Pedestrians'], 1)
p = np.poly1d(z)
axes[0].plot(df_corr['temperature'], p(df_corr['temperature']), "r--", alpha=0.8, linewidth=2)

# Precipitation vs Pedestrians
axes[1].scatter(df_corr['precipitation'], df_corr['Pedestrians'], alpha=0.5)
axes[1].set_xlabel('Precipitation (inches)')
axes[1].set_ylabel('Pedestrian Count')
axes[1].set_title('Precipitation vs Pedestrian Count')
axes[1].grid(True, alpha=0.3)

plt.tight_layout()
plt.show()

# Summary insights
print("\n=== KEY INSIGHTS ===")
print(f"Correlation between Temperature and Pedestrians: {correlation_matrix.loc['temperature', 'Pedestrians']:.3f}")
print(f"Correlation between Precipitation and Pedestrians: {correlation_matrix.loc['precipitation', 'Pedestrians']:.3f}")

if correlation_matrix.loc['temperature', 'Pedestrians'] > 0:
    print("→ Positive correlation: Higher temperatures tend to increase pedestrian activity")
else:
    print("→ Negative correlation: Higher temperatures tend to decrease pedestrian activity")
    
if correlation_matrix.loc['precipitation', 'Pedestrians'] < 0:
    print("→ Negative correlation: More precipitation tends to decrease pedestrian activity")
else:
    print("→ Positive correlation: More precipitation tends to increase pedestrian activity")












########## Question 3 ##########

Here's the code to create a custom function for time categorization and analyze pedestrian patterns:
pythonimport pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

# Load the data
url = 'https://data.cityofnewyork.us/api/views/6fi9-q3ta/rows.csv?accessType=DOWNLOAD'
df = pd.read_csv(url)

# Convert hour_beginning to datetime
df['hour_beginning'] = pd.to_datetime(df['hour_beginning'])

# Filter for Brooklyn Bridge and 2019
df_2019 = df[(df['hour_beginning'].dt.year == 2019) & (df['location'] == 'Brooklyn Bridge')]

# Custom function to categorize time of day
def categorize_time_of_day(hour):
    """
    Categorizes hour into time of day periods:
    - Morning: 6 AM to 11:59 AM
    - Afternoon: 12 PM to 5:59 PM
    - Evening: 6 PM to 8:59 PM
    - Night: 9 PM to 5:59 AM
    """
    if 6 <= hour < 12:
        return 'Morning'
    elif 12 <= hour < 18:
        return 'Afternoon'
    elif 18 <= hour < 21:
        return 'Evening'
    else:
        return 'Night'

# Extract hour and apply the custom function
df_2019['hour'] = df_2019['hour_beginning'].dt.hour
df_2019['time_of_day'] = df_2019['hour'].apply(categorize_time_of_day)

# Display sample of the new column
print("=== Sample Data with Time of Day Category ===")
print(df_2019[['hour_beginning', 'hour', 'time_of_day', 'Pedestrians']].head(10))

# Analyze pedestrian activity by time of day
print("\n=== Pedestrian Activity Statistics by Time of Day ===")
time_stats = df_2019.groupby('time_of_day')['Pedestrians'].agg([
    'count', 'mean', 'median', 'std', 'min', 'max'
]).round(2)

# Reorder for logical flow
time_order = ['Morning', 'Afternoon', 'Evening', 'Night']
time_stats = time_stats.reindex(time_order)
print(time_stats)

# Calculate total pedestrians by time of day
print("\n=== Total Pedestrians by Time of Day ===")
total_by_time = df_2019.groupby('time_of_day')['Pedestrians'].sum().reindex(time_order)
print(total_by_time)

# Calculate percentage distribution
print("\n=== Percentage Distribution of Pedestrian Activity ===")
percentage_dist = (total_by_time / total_by_time.sum() * 100).round(2)
print(percentage_dist)

# Visualization 1: Bar plot of average pedestrians by time of day
plt.figure(figsize=(10, 6))
avg_by_time = df_2019.groupby('time_of_day')['Pedestrians'].mean().reindex(time_order)
sns.barplot(x=time_order, y=avg_by_time.values, palette='viridis')
plt.title('Average Pedestrian Count by Time of Day\nBrooklyn Bridge 2019', fontsize=14, fontweight='bold')
plt.xlabel('Time of Day', fontsize=12)
plt.ylabel('Average Pedestrian Count', fontsize=12)
plt.grid(axis='y', alpha=0.3)
plt.tight_layout()
plt.show()

# Visualization 2: Box plot showing distribution
plt.figure(figsize=(12, 6))
sns.boxplot(data=df_2019, x='time_of_day', y='Pedestrians', order=time_order, palette='Set2')
plt.title('Distribution of Pedestrian Counts by Time of Day\nBrooklyn Bridge 2019', fontsize=14, fontweight='bold')
plt.xlabel('Time of Day', fontsize=12)
plt.ylabel('Pedestrian Count', fontsize=12)
plt.grid(axis='y', alpha=0.3)
plt.tight_layout()
plt.show()

# Visualization 3: Pie chart of total pedestrian distribution
plt.figure(figsize=(8, 8))
colors = ['#ff9999', '#66b3ff', '#99ff99', '#ffcc99']
plt.pie(total_by_time.values, labels=time_order, autopct='%1.1f%%', 
        colors=colors, startangle=90, textprops={'fontsize': 12})
plt.title('Distribution of Total Pedestrian Activity by Time of Day\nBrooklyn Bridge 2019', 
          fontsize=14, fontweight='bold')
plt.tight_layout()
plt.show()

# Visualization 4: Hourly pattern within each time of day category
plt.figure(figsize=(14, 6))
hourly_avg = df_2019.groupby('hour')['Pedestrians'].mean()
colors_map = {
    'Night': '#ffcc99',
    'Morning': '#ff9999', 
    'Afternoon': '#66b3ff',
    'Evening': '#99ff99'
}
hour_colors = [colors_map[categorize_time_of_day(h)] for h in range(24)]

bars = plt.bar(range(24), hourly_avg.values, color=hour_colors, edgecolor='black', linewidth=0.5)
plt.title('Average Pedestrian Count by Hour of Day\nBrooklyn Bridge 2019', fontsize=14, fontweight='bold')
plt.xlabel('Hour of Day', fontsize=12)
plt.ylabel('Average Pedestrian Count', fontsize=12)
plt.xticks(range(24))
plt.grid(axis='y', alpha=0.3)

# Add legend
from matplotlib.patches import Patch
legend_elements = [Patch(facecolor=colors_map[time], label=time) for time in time_order]
plt.legend(handles=legend_elements, loc='upper right')
plt.tight_layout()
plt.show()

`plt.figure(figsize=(12, 6))
sns.violinplot(data=df_2019, x='time_of_day', y='Pedestrians', order=time_order, palette='muted')
plt.title('Detailed Distribution of Pedestrian Counts by Time of Day\nBrooklyn Bridge 2019', 
          fontsize=14, fontweight='bold')
plt.xlabel('Time of Day', fontsize=12)
plt.ylabel('Pedestrian Count', fontsize=12)
plt.grid(axis='y', alpha=0.3)
plt.tight_layout()
plt.show()

# Peak hours analysis
print("\n=== Peak Hours Analysis ===")
top_5_hours = df_2019.groupby('hour')['Pedestrians'].mean().sort_values(ascending=False).head(5)
print("Top 5 busiest hours:")
for hour, count in top_5_hours.items():
    time_cat = categorize_time_of_day(hour)
    print(f"  {hour}:00 ({time_cat}): {count:.2f} average pedestrians")

# Quietest hours
print("\nTop 5 quietest hours:")
bottom_5_hours = df_2019.groupby('hour')['Pedestrians'].mean().sort_values().head(5)
for hour, count in bottom_5_hours.items():
    time_cat = categorize_time_of_day(hour)
    print(f"  {hour}:00 ({time_cat}): {count:.2f} average pedestrians")

# Summary insights
print("\n=== KEY INSIGHTS ===")
busiest_time = avg_by_time.idxmax()
quietest_time = avg_by_time.idxmin()
print(f"→ Busiest time of day: {busiest_time} ({avg_by_time[busiest_time]:.2f} avg pedestrians)")
print(f"→ Quietest time of day: {quietest_time} ({avg_by_time[quietest_time]:.2f} avg pedestrians)")
print(f"→ Peak activity is {(avg_by_time[busiest_time] / avg_by_time[quietest_time]):.2f}x higher than lowest activity")
