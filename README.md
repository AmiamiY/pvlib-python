from datetime import datetime, timedelta
import pvlib
import matplotlib.pyplot as plt
import numpy as np

# Define the location
latitude = 35.82272
longitude = 139.575548
timezone = 'Asia/Tokyo'

# Time ranges for each case
time_range1 = [datetime(2024, 9, 16, 13, 0) + timedelta(minutes=30 * i) for i in range(9)]
time_range2 = [datetime(2024, 11, 30, 12, 0) + timedelta(minutes=30 * i) for i in range(9)]

# Calculate solar positions
def calculate_solar_positions(times, latitude, longitude, timezone):
    solar_positions = []
    for time in times:
        solpos = pvlib.solarposition.get_solarposition(
            time, latitude, longitude, altitude=0, tz=timezone)
        solar_positions.append((solpos['azimuth'].values[0], solpos['apparent_elevation'].values[0]))
    return solar_positions

# Get solar positions for each range
solar_positions1 = calculate_solar_positions(time_range1, latitude, longitude, timezone)
solar_positions2 = calculate_solar_positions(time_range2, latitude, longitude, timezone)

# Extract azimuth and elevation for plotting
azimuth1, elevation1 = zip(*solar_positions1)
azimuth2, elevation2 = zip(*solar_positions2)

# Convert azimuth and elevation to radians for polar plotting
azimuth1_rad = np.radians(azimuth1)
azimuth2_rad = np.radians(azimuth2)
elevation1 = np.array(elevation1)
elevation2 = np.array(elevation2)

# Create a polar plot
fig, ax = plt.subplots(subplot_kw={'projection': 'polar'}, figsize=(12, 8))

# Plot the solar positions for each range
ax.plot(azimuth1_rad, 90 - elevation1, label="Sept 16, 13:00-17:00", marker='o', color='orange')
ax.plot(azimuth2_rad, 90 - elevation2, label="Nov 30, 12:00-16:00", marker='x', color='blue')

# Reverse radial axis to show altitude correctly
ax.set_ylim(90, 0)

# Labels and legend
ax.set_title('Comparison of Sunlight Angles and Positions', va='bottom')
ax.legend(loc='lower left', bbox_to_anchor=(1.1, 0.5))

plt.show()
