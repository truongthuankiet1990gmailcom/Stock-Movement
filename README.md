# Stock Movement Prediction
<img src = "Images/dataset_cover.png">

This project predicts stock price movements using various machine learning and deep learning models, including LSTM, CNN, Bidirectional LSTM, and Linear Regression. The dataset used for this project is `FPT.csv`.

## Table of Contents
- [Project Overview](#project-overview)
- [Dataset](#dataset)
- [Preprocessing](#preprocessing)
- [Feature Engineering](#feature-engineering)
- [Models](#models)
- [Evaluation Metrics](#evaluation-metrics)
- [Results](#result)


## Project Overview
The goal of this project is to predict stock price changes based on historical data. The project includes data preprocessing, feature engineering, and training multiple models to evaluate their performance. In this project, instead of predicting the value of stock price, I would used the movement of stock prices among days, because in reality, what we would expect is to know whether the stock price goes up or down.

## Dataset
The dataset `FPT.csv` contains stock data with the following columns:
- `Date/Time`: Timestamp of the data point.
- `Open`: Opening price.
<img src = "Images/open.png">

- `High`: Highest price.
- `Low`: Lowest price.
<img src = "Images/low_high.png">

- `Close`: Closing price.
<img src = "Images/close.png">

- `Volume`: Trading volume.
<img src = "Images/volume.png">

- `Open Interest`: (Dropped during preprocessing).

## Preprocessing
- Dropped unnecessary columns like `Open Interest` and `Ticker`.
- Create `target` feature here is calculated by the close price of previous day and current day.
- Added technical indicators such as **RSI** and **EMA**, because:
    - **SI (Relative Strength Index)**
        - Purpose: RSI measures the speed and change of price movements. It is a momentum oscillator that ranges from 0 to 100.
        - Why Add It?
            - RSI helps identify overbought or oversold conditions in the stock market.
            Values above 70 typically indicate overbought conditions, while values below 30 indicate oversold conditions.
            - Including RSI as a feature allows the model to capture momentum trends and reversals in stock prices, which are critical for predicting price movements.
    - **EMA (Exponential Moving Average)**
        - Purpose: EMA is a type of moving average that gives more weight to recent prices, making it more responsive to new information compared to a simple moving average.
        - Why Add It?
            - EMA helps smooth out price data to identify trends more clearly.
            - Different EMAs (e.g., fast, medium, slow) capture short-term, medium-term, and long-term trends, respectively.
            - By adding multiple EMAs (e.g., 20-day, 100-day, 150-day), the model can better understand the overall trend and detect potential trend reversals.

- Scaled the data using `MinMaxScaler`, because:
    - Min-Max Scaling is preferred here because the data (e.g., stock prices) does not follow a normal distribution, and the range of values is more important for time-series models like LSTM and CNN.
    - Ensures that all features contribute equally to the learning process, preventing features with larger ranges (e.g., Volume) from dominating those with smaller ranges (e.g., RSI).
- Created sequences of past data for time-series modeling.


## Feature Engineering
- Added new features like RSI, EMA (fast, medium, slow), and target variables.
- Shifted the `Close` price to create a target for prediction.
- Filled missing values with the median.

## Models

The project implements and evaluates multiple machine learning and deep learning models to predict stock price movements. Below are the details of how each model was developed:

### 1. Long Short-Term Memory (LSTM)
- **Reason for chosen:**
    - LSTMs are specifically designed for sequential data, making them ideal for time-series forecasting like stock price movements.
    - They can capture long-term dependencies and patterns in the data, which is crucial for understanding trends in stock prices over time.
    - The ability to handle vanishing gradient problems makes LSTMs effective for learning from long sequences of historical data.
- **Input Shape**: The LSTM model was designed to take sequences of 60 past days (`back_days`) as input, with 9 features (e.g., Open, High, Low, Volume, RSI, EMA).
- **Architecture**:
  - Three stacked LSTM layers with 100 units each.
  - LeakyReLU activation to avoid the "dying ReLU" problem.
  - Dropout layers (30%) to prevent overfitting.
  - Batch normalization to stabilize and accelerate training.
  - A dense layer with 50 units followed by the output layer with a single neuron for regression.
- **Loss Function**: Mean Absolute Percentage Error (MAPE).
- **Optimizer**: Adam optimizer with a learning rate of 0.001.
- **Training**: Early stopping was used to monitor validation loss and prevent overfitting.

### 2. Convolutional Neural Network (CNN)
- **Reason for chosen:**
    - CNNs are excellent at feature extraction, even for time-series data.
    - They can identify local patterns (e.g., short-term trends or spikes) in the data by applying convolutional filters.
    - By stacking multiple convolutional layers, the model can learn hierarchical patterns, which are useful for understanding complex stock price movements.
- **Input Shape**: Similar to the LSTM model, the CNN model used sequences of 60 past days with 9 features.
- **Architecture**:
  - Three convolutional layers with 128, 128, and 64 filters, respectively, and a kernel size of 3.
  - MaxPooling layers to reduce dimensionality and extract important features.
  - Dropout layers (30%) and batch normalization for regularization and stability.
  - Flatten layer to convert the 3D tensor into a 1D vector.
  - Two dense layers with 100 and 50 units, followed by the output layer.
- **Loss Function**: Mean Absolute Percentage Error (MAPE).
- **Optimizer**: Adam optimizer with a learning rate of 0.0005.
- **Training**: Early stopping was applied to monitor validation loss.

### 3. Bidirectional LSTM
- **Reason for chosen:**
    - Unlike standard LSTMs, Bidirectional LSTMs process the input sequence in both forward and backward directions.
    - This allows the model to learn from both past and future contexts, which is particularly useful for stock price prediction, where future trends can influence current predictions.
    - It enhances the model's ability to capture relationships in the data that might not be apparent when looking only at past values.
- **Input Shape**: The same as the LSTM model.
- **Architecture**:
  - Three Bidirectional LSTM layers with 100 units each, allowing the model to learn from both past and future contexts.
  - LeakyReLU activation, dropout layers (30%), and batch normalization for regularization and stability.
  - A dense layer with 50 units followed by the output layer.
- **Loss Function**: Mean Absolute Percentage Error (MAPE).
- **Optimizer**: Adam optimizer with a learning rate of 0.001.
- **Training**: Early stopping was used to monitor validation loss.

### 4. Linear Regression
- **Reason for chosen**:
    - Base model.
    - Simple and interpretable, providing a benchmark to evaluate the performance of more complex models.
- **Input Shape**: The input data was reshaped into a 2D array for compatibility with the linear regression model.
- **Development**:
  - A simple linear regression model was trained using the `LinearRegression` class from `sklearn`.
  - The model was used as a baseline to compare the performance of deep learning models.
- **Evaluation**: The model's predictions were compared against the actual values using RMSE, MAPE, MAE, and R² metrics.

### Model Training and Evaluation
- **Data Splitting**: The dataset was split into 80% training and 20% testing.
- **Scaling**: Features were scaled using `MinMaxScaler` to normalize the data between 0 and 1.
- **Metrics**: The models were evaluated using the following metrics:
  - **RMSE**: Measures the average magnitude of errors.
  - **MAPE**: Measures prediction accuracy as a percentage. (This is the main metric I focused, because it is well-suited for evaluating regression models in financial forecasting, also it focuses on the relative error rather than the absolute error, which is important when predicting stock price movements)
  - **MAE**: Measures the average magnitude of errors.
  - **R²**: Measures how well the model explains the variance in the data.

#### LSTM
<img src = "Images/LSTM.png">

#### Linear regression
<img src ="Images/linear.png">

#### CNN
<img src = "Images/cnn.png">

#### Bi LSTM
<img src = "Images/bilstm.png">

#### Evaluation
## Model Evaluation Metrics

| Model                | RMSE   | MAPE (%) | MAE    | R²      |
|----------------------|--------|----------|--------|---------|           
| **CNN**             | 1.5036512046785688   | inf     | 1.3633113405386952   | -452.81371555022724    |
| **Bidirectional LSTM** | 0.2045173024994082   | inf     | 0.18940485228605006   | -7.395441041903794    |
| **Linear Regression** | 0.6509151776500515   | inf     | 0.6471449773597199   | -84.04175239245642    |

- Here, MAPE is inf which is understandable since I already min-max scaled target, therefore some target values are negative.
- Bidirectional LSTM is the best-performing model based on RMSE and MAE. However, its negative R² indicates that there is still room for improvement.
- CNN performs the worst, with the highest RMSE, MAE, and an extremely negative R². This suggests that the architecture may not be suitable for this task or requires significant tuning.
- Linear Regression, as a baseline model, performs better than CNN but is outperformed by Bidirectional LSTM.


### Future Improvements
- Experiment with additional architectures like GRU or Transformer models.
- Perform hyperparameter tuning using grid search or Bayesian optimization.
- Incorporate external data (e.g., news sentiment, macroeconomic indicators) to improve predictions.

## Results
The Bidirectional LSTM shows the most promise, but improvements are needed to address the MAPE and R² issues. CNN and Linear Regression require significant adjustments or may not be suitable for this task without further tuning.