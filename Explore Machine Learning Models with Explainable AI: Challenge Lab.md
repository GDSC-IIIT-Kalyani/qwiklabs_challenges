# Explore Machine Learning Models with Explainable AI: Challenge Lab

> Launch the lab [here](https://google.qwiklabs.com/focuses/12011?parent=catalog)

## Your challenge

You are a curious coder who wants to explore biases in public datasets using the What-If Tool. You decide to pull some mortgage [data](https://www.consumerfinance.gov/data-research/hmda/historic-data/) to train a couple of machine learning models to predict whether an applicant will be granted a loan. You specifically want to investigate how the two models perform when they are trained on different proportions of males and females in the datasets, and visualize their differences in the What-If Tool.

### Start a JupyterLab Notebook instance

* Storage > Bucket > Create Bucket > Enter the name as your **Project ID** > CREATE

* AI Platform > Notebooks > NEW INSTANCE > Use the latest version of TensorFlow > CREATE

* Wait for it to be created > **Open Jupyterlab**

### Download the Challenge Notebook

* Clone the repo

* Open the Juputer Notebook file as per the lab instruction

### Build and train your models

* Modify the block 10 as:

```python
model = Sequential() 
model.add(layers.Dense(200, input_shape=(input_size,), activation='relu'))
model.add(layers.Dense(50, activation='relu'))
model.add(layers.Dense(20, activation='relu'))
model.add(layers.Dense(1, activation='sigmoid'))

# The data will come from train_data and train_labels.
model.compile(loss='mean_squared_error', optimizer='adam', metrics=['accuracy'])
model.fit(train_data, train_labels, epochs=10, batch_size=2048, validation_split=0.1)
```

* Modify the block 13:

```python
limited_model = Sequential()
limited_model.add(layers.Dense(200, input_shape=(input_size,), activation='relu'))
limited_model.add(layers.Dense(50, activation='relu'))
limited_model.add(layers.Dense(20, activation='relu'))
limited_model.add(layers.Dense(1, activation='sigmoid'))
limited_model.compile(loss='mean_squared_error', optimizer='adam', metrics=['accuracy'])

# The data will come from limited_train_data and limited_train_labels.
limited_model.fit(limited_train_data, limited_train_labels, epochs=10, batch_size=2048, validation_split=0.1)
```

### Deploy the models to AI Platform

* Enter the peoper information block 16. Change the saved_limited_model to limited_model and saved_completed_model to completed_model .

* Block 19:

```python
!gcloud ai-platform models create $MODEL_NAME --regions $REGION
```

* Block 20:

```python
!gcloud ai-platform versions create $VERSION_NAME \
--model=$MODEL_NAME \
--framework='Tensorflow' \
--runtime-version=2.1 \
--origin=$MODEL_BUCKET/completed_model \
--staging-bucket=$MODEL_BUCKET \
--python-version=3.7
```

* Block 21:

```python
!gcloud ai-platform models create $LIM_MODEL_NAME --regions $REGION
```

* Block 22:

```python
!gcloud ai-platform versions create $VERSION_NAME \
--model=$LIM_MODEL_NAME \
--framework='Tensorflow' \
--runtime-version=2.1 \
--origin=$MODEL_BUCKET/limited_model \
--staging-bucket=$MODEL_BUCKET \
--python-version=3.7
```

### Use the What-If Tool to explore biases

Just run the code for What-If Tool.
