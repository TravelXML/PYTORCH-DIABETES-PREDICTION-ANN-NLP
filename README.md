# Creating ANN with PyTorch on Pima Diabetes Dataset

This repository showcases the creation of an Artificial Neural Network (ANN) using PyTorch to predict diabetes based on the Pima Indian Diabetes Dataset. The project covers all essential steps, from data preprocessing to model evaluation, and also provides a Docker setup for easy replication.

## Table of Contents
1. [Project Overview](#project-overview)
2. [Project Structure](#project-structure)
3. [Getting Started](#getting-started)
   - [Prerequisites](#prerequisites)
   - [Installation](#installation)
4. [Running with Docker](#running-with-docker)
   - [Build the Docker Image](#1-build-the-docker-image)
   - [Run the Docker Container](#2-run-the-docker-container)
   - [Access Jupyter Notebook](#3-access-jupyter-notebook)
5. [Key Steps in the Project](#key-steps-in-the-project)
   - [Data Preprocessing](#1-data-preprocessing)
   - [Feature and Target Separation](#2-feature-and-target-separation)
   - [Data Splitting](#3-data-splitting)
   - [Tensor Conversion](#4-tensor-conversion)
   - [Model Definition](#5-model-definition)
   - [Model Instantiation](#6-model-instantiation)
   - [Loss Function and Optimizer](#7-loss-function-and-optimizer)
   - [Model Training](#8-model-training)
   - [Model Evaluation Mode](#9-model-evaluation-mode)
   - [Predictions](#10-predictions)
   - [Confusion Matrix](#11-confusion-matrix)
   - [Confusion Matrix Visualization](#12-confusion-matrix-visualization)
   - [Accuracy Calculation](#13-accuracy-calculation)
6. [Results](#results)


## Project Overview

- **Data Preprocessing:** Loaded the dataset, handled missing values, and labeled the target variable.
- **Modeling:** Built a custom neural network with PyTorch, trained it, and evaluated its performance.
- **Visualization:** Visualized the results using a confusion matrix heatmap.

## Project Structure

- **data/**: Contains the dataset used in the project.
- **notebooks/**: Jupyter notebooks with the code and explanations.
- **requirements.txt**: Python dependencies required to run the project.
- **README.md**: Overview of the project (this file).

## Getting Started

### Prerequisites

Make sure you have Python 3.x and `pip` installed on your system.

### Installation

1. Clone the repository:
    ```bash
    git clone https://github.com/your-username/PYTORCH-DIABETES-PREDICTION.git
    cd PYTORCH-DIABETES-PREDICTION
    ```

2. Install the required Python packages:
    ```bash
    pip install -r requirements.txt
    ```

3. Run the Jupyter notebook:
    ```bash
    jupyter notebook notebooks/Creating_ANN_with_PyTorch.ipynb
    ```

## Running with Docker

### 1. Build the Docker Image
Build the Docker image using the Dockerfile:

```bash
docker build -t pytorch-jupyter .
```

### 2. Run the Docker Container
Run a container from the image you just built:

```bash
docker run -it --name pytorch-jupyter-container -p 8888:8888 pytorch-jupyter
```

### 3. Access Jupyter Notebook
After running the container, you will see a URL in the terminal with a token, something like this:

```bash
http://127.0.0.1:8888/?token=<your-token-here>
```

## Key Steps in the Project

### 1. Data Preprocessing

Loaded the dataset, checked for missing values, and replaced the numeric target variable with descriptive labels ("Diabetic" and "No Diabetic").

```python
import pandas as pd

# Load the dataset
df = pd.read_csv('data/diabetes.csv')

# Check for missing values
print(df.isnull().sum())

# Replace numeric target with descriptive labels
df['Outcome'] = df['Outcome'].replace({1: "Diabetic", 0: "No Diabetic"})
```

### 2. Feature and Target Separation

Split the DataFrame into independent features (`X`) and the target variable (`y`).

```python
# Separate features and target variable
X = df.drop('Outcome', axis=1).values
y = df['Outcome'].values
```

### 3. Data Splitting

Divided the dataset into training and testing sets using an 80-20 split.

```python
from sklearn.model_selection import train_test_split

# Split data into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
```

### 4. Tensor Conversion

Converted the training and testing data into PyTorch tensors for model training.

```python
import torch

# Convert data to PyTorch tensors
X_train = torch.FloatTensor(X_train)
X_test = torch.FloatTensor(X_test)
y_train = torch.LongTensor(y_train)
y_test = torch.LongTensor(y_test)
```

### 5. Model Definition

Created a custom neural network model class (`ANN_Model`) with two hidden layers using PyTorch.

```python
import torch.nn as nn
import torch.nn.functional as F

# Define the neural network model
class ANN_Model(nn.Module):
    def __init__(self, input_features=8, hidden1=20, hidden2=20, out_features=2):
        super().__init__()
        self.f_connected1 = nn.Linear(input_features, hidden1)
        self.f_connected2 = nn.Linear(hidden1, hidden2)
        self.out = nn.Linear(hidden2, out_features)
    
    def forward(self, x):
        x = F.relu(self.f_connected1(x))
        x = F.relu(self.f_connected2(x))
        x = self.out(x)
        return x
```

### 6. Model Instantiation

Initialized the model and set the random seed for reproducibility.

```python
# Set the random seed for reproducibility
torch.manual_seed(20)

# Instantiate the model
model = ANN_Model()
```

### 7. Loss Function and Optimizer

Defined the loss function (`CrossEntropyLoss`) and the optimizer (`Adam`) to guide the model training.

```python
# Define the loss function and optimizer
loss_function = nn.CrossEntropyLoss()
optimizer = torch.optim.Adam(model.parameters(), lr=0.01)
```

### 8. Model Training

Trained the model over 500 epochs, recording the loss at each epoch and updating the model's parameters using backpropagation.

```python
# Train the model
epochs = 500
final_losses = []

for i in range(epochs):
    i = i + 1
    y_pred = model.forward(X_train)
    loss = loss_function(y_pred, y_train)
    final_losses.append(loss)
    if i % 10 == 1:
        print(f"Epoch number: {i} and the loss: {loss.item()}")
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
```

### 9. Model Evaluation Mode

Set the model to evaluation mode to ensure correct behavior during testing.

```python
# Set the model to evaluation mode
model.eval()
```

### 10. Predictions

Made predictions on the test data and stored them.

```python
# Make predictions on the test data
predictions = []
with torch.no_grad():
    for i, data in enumerate(X_test):
        y_pred = model(data)
        predictions.append(y_pred.argmax().item())
```

### 11. Confusion Matrix

Calculated the confusion matrix to evaluate the performance of the model's predictions.

```python
from sklearn.metrics import confusion_matrix

# Calculate the confusion matrix
cm = confusion_matrix(y_test, predictions)
print(cm)
```

### 12. Confusion Matrix Visualization

Visualized the confusion matrix using a heatmap to better understand prediction accuracy.

```python
import seaborn as sns
import matplotlib.pyplot as plt

# Visualize the confusion matrix
plt.figure(figsize=(10,6))
sns.heatmap(cm, annot=True, fmt="d")
plt.xlabel('Actual Values')
plt.ylabel('Predicted Values')
plt.show()
```

### 13. Accuracy Calculation

Computed the accuracy of the model's predictions on the test data.

```python
from sklearn.metrics import accuracy_score

# Calculate the accuracy score
score = accuracy_score(y_test, predictions)
print(f'Accuracy: {score}')
```

## Results

![image](https://github.com/user-attachments/assets/1aee5981-f78a-47f3-b96b-496e06204f10)

![image](https://github.com/user-attachments/assets/c96fb101-4bc0-44ab-b8f9-c042b473b576)

![image](https://github.com/user-attachments/assets/f0c505fa-32b3-4dfd-9304-1b6e31736b4c)






