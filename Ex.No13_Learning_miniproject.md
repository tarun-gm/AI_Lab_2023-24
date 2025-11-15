# Ex.No: 10 Learning – Use Supervised Learning  
### DATE: 15/11/2025                                                                           
### REGISTER NUMBER : 212223060284  

### AIM:  
To develop a supervised learning based Temple Crowd Management model using Random Forest Classifier to predict crowd level and suggest safe routes for devotees inside the temple using a graph-based temple map.

### Algorithm:

## 1. Data Simulation and Loading
- Simulate a synthetic dataset representing temple context with features such as time of day, weekend/weekday, festival flag, and entry rate.
- Store the data in a pandas DataFrame with a target column indicating crowd level (Low, Medium, High).

## 2. Data Preprocessing
- Select the input features: time_of_day, is_weekend, is_festival, entry_rate.
- Define the target variable as crowd_level.
- Split the dataset into training and testing sets using an 80:20 ratio.

## 3. Model Training (Supervised Learning)
- Initialize a RandomForestClassifier model.
- Train the classifier on the training data (X_train, y_train).
- Predict crowd level on the test data (X_test) to obtain y_pred.

## 4. Model Evaluation
- Evaluate the classifier using:
  - Classification report (precision, recall, F1-score).
  - Confusion matrix to understand prediction accuracy for each class.

## 5. Temple Graph Construction
- Create a graph using NetworkX where nodes represent temple locations (Entry, Security, Main_Hall, Inner_Hall, Darshan_Point, Exit, Comfort_Pod, Medical).
- Add weighted edges between nodes to represent walking paths and their base costs.

## 6. Scenario-Based Crowd Prediction
- Define a few sample temple scenarios (e.g., Morning normal day, Evening weekend, Night festival) with feature values.
- Use the trained classifier to predict the crowd level for each scenario.

## 7. Devotee Route Planning
- For each devotee, assign a health status (normal, senior, emergency), a current location, and a scenario.
- Based on predicted crowd level:
  - If crowd is High, increase weight of critical edges to avoid congested paths.
  - Compute shortest path for:
    - Normal devotees → to Darshan_Point.
    - Senior devotees → via Comfort_Pod to Darshan_Point.
    - Emergency devotees → via Medical to Exit.

## 8. Output and Visualization
- Print classification metrics and devotee-specific routes with predicted crowd levels.
- Plot the temple graph using a fixed layout for clear visualization of nodes and paths.

Stop.

### Program:

```
python
import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import classification_report, confusion_matrix
import networkx as nx
import matplotlib.pyplot as plt

np.random.seed(42)

n_samples = 500
time_of_day = np.random.randint(0, 24, n_samples)
is_weekend = np.random.randint(0, 2, n_samples)
is_festival = np.random.randint(0, 2, n_samples)
entry_rate = np.random.randint(50, 500, n_samples)

crowd_score = entry_rate + is_festival * 200 + is_weekend * 100 + (np.abs(time_of_day - 12) * 10)
crowd_level = pd.cut(crowd_score, bins=[0, 800, 1200, 2000], labels=["Low", "Medium", "High"])

df = pd.DataFrame({
    "time_of_day": time_of_day,
    "is_weekend": is_weekend,
    "is_festival": is_festival,
    "entry_rate": entry_rate,
    "crowd_level": crowd_level
})

X = df[["time_of_day", "is_weekend", "is_festival", "entry_rate"]]
y = df["crowd_level"]

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42, stratify=y)

model = RandomForestClassifier(n_estimators=100, random_state=42)
model.fit(X_train, y_train)

y_pred = model.predict(X_test)
print(classification_report(y_test, y_pred))
print(confusion_matrix(y_test, y_pred))

G = nx.Graph()
nodes = ["Entry", "Security", "Main_Hall", "Inner_Hall", "Darshan_Point", "Exit", "Comfort_Pod", "Medical"]
G.add_nodes_from(nodes)

edges = [
    ("Entry", "Security", 2),
    ("Security", "Main_Hall", 3),
    ("Main_Hall", "Inner_Hall", 4),
    ("Inner_Hall", "Darshan_Point", 2),
    ("Darshan_Point", "Exit", 5),
    ("Main_Hall", "Comfort_Pod", 2),
    ("Comfort_Pod", "Exit", 3),
    ("Inner_Hall", "Medical", 2),
    ("Medical", "Exit", 2),
]

for u, v, w in edges:
    G.add_edge(u, v, weight=w)

sample_context = pd.DataFrame({
    "time_of_day": [10, 18, 20],
    "is_weekend": [0, 1, 1],
    "is_festival": [0, 0, 1],
    "entry_rate": [150, 300, 450]
}, index=["Morning_Normal_Day", "Evening_Weekend", "Night_Festival"])

crowd_pred = model.predict(sample_context)
scenario_to_crowd = dict(zip(sample_context.index, crowd_pred))

devotees = [
    {"name": "Devotee_A", "health": "normal", "location": "Security", "scenario": "Morning_Normal_Day"},
    {"name": "Devotee_B", "health": "senior", "location": "Main_Hall", "scenario": "Evening_Weekend"},
    {"name": "Devotee_C", "health": "emergency", "location": "Inner_Hall", "scenario": "Night_Festival"},
]

def best_route(devotee):
    source = devotee["location"]
    crowd = scenario_to_crowd[devotee["scenario"]]
    H = G.copy()
    if crowd == "High":
        if H.has_edge("Main_Hall", "Inner_Hall"):
            H["Main_Hall"]["Inner_Hall"]["weight"] = 999
    try:
        if devotee["health"] == "senior":
            path1 = nx.shortest_path(H, source, "Comfort_Pod", weight="weight")
            path2 = nx.shortest_path(H, "Comfort_Pod", "Darshan_Point", weight="weight")
            route = path1 + path2[1:]
        elif devotee["health"] == "emergency":
            path1 = nx.shortest_path(H, source, "Medical", weight="weight")
            path2 = nx.shortest_path(H, "Medical", "Exit", weight="weight")
            route = path1 + path2[1:]
        else:
            route = nx.shortest_path(H, source, "Darshan_Point", weight="weight")
    except nx.NetworkXNoPath:
        route = [source, "WAIT_ZONE"]
    return route, crowd

for d in devotees:
    route, crowd = best_route(d)
    print(d["name"], "Scenario:", d["scenario"], "Predicted crowd:", crowd, "Route:", " -> ".join(route))

pos = {
    "Entry": (0, 5),
    "Security": (2, 5),
    "Main_Hall": (4, 5),
    "Inner_Hall": (6, 5),
    "Darshan_Point": (8, 5),
    "Exit": (10, 5),
    "Comfort_Pod": (4, 3),
    "Medical": (6, 3),
}

plt.figure(figsize=(10, 6))
nx.draw(G, pos, with_labels=True, node_color="lightblue", node_size=2500, font_size=10, font_weight="bold")
plt.title("Temple Map Graph")
plt.axis("off")
plt.show()
```
# OUTPUT 
<img width="919" height="742" alt="download (5)" src="https://github.com/user-attachments/assets/0a676ecb-3007-4ed4-b702-a7a58b68c8ac" />
<img width="563" height="435" alt="download (6)" src="https://github.com/user-attachments/assets/6f85e5f4-2adf-40fc-b100-5d83136ac9e1" />
<img width="828" height="398" alt="download (7)" src="https://github.com/user-attachments/assets/7e7afcc8-56f8-4c1d-8f50-32c09b5c0088" />



# RESULT
Thus, the supervised learning model for temple crowd level prediction was trained successfully, and the system suggested suitable routes for different devotees based on predicted crowd conditions.
