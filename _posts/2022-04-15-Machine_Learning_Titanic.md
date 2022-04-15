---
layout: post
title: Machine Learning - Titanic
subtitle: Titanic Predictions
categories: Machine Learning
tags: Machine Learning
use_math: true
---

## Machine Learning - Titanic Prediction

[Kaggle에 업로드한 코드](https://www.kaggle.com/code/shimjh/titanic-assignment)


{
 "cells": [
  {
   "cell_type": "markdown",
   "id": "152f71fc",
   "metadata": {
    "papermill": {
     "duration": 0.025345,
     "end_time": "2022-04-11T12:52:39.136225",
     "exception": false,
     "start_time": "2022-04-11T12:52:39.110880",
     "status": "completed"
    },
    "tags": []
   },
   "source": [
    "## 데이터 읽기 및 분석"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "id": "3de0afff",
   "metadata": {
    "_cell_guid": "b1076dfc-b9ad-4769-8c92-a6c4dae69d19",
    "_uuid": "8f2839f25d086af736a60e9eeb907d3b93b6e0e5",
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:39.197886Z",
     "iopub.status.busy": "2022-04-11T12:52:39.190937Z",
     "iopub.status.idle": "2022-04-11T12:52:39.225967Z",
     "shell.execute_reply": "2022-04-11T12:52:39.225161Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.078434Z"
    },
    "papermill": {
     "duration": 0.063065,
     "end_time": "2022-04-11T12:52:39.226140",
     "exception": false,
     "start_time": "2022-04-11T12:52:39.163075",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "import numpy as np \n",
    "import pandas as pd \n",
    "import os\n",
    "\n",
    "train = pd.read_csv(\"/kaggle/input/titanic/train.csv\")\n",
    "test = pd.read_csv(\"/kaggle/input/titanic/test.csv\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "id": "699b740c",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:39.284295Z",
     "iopub.status.busy": "2022-04-11T12:52:39.283632Z",
     "iopub.status.idle": "2022-04-11T12:52:39.302472Z",
     "shell.execute_reply": "2022-04-11T12:52:39.302975Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.098443Z"
    },
    "papermill": {
     "duration": 0.052446,
     "end_time": "2022-04-11T12:52:39.303147",
     "exception": false,
     "start_time": "2022-04-11T12:52:39.250701",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>PassengerId</th>\n",
       "      <th>Survived</th>\n",
       "      <th>Pclass</th>\n",
       "      <th>Name</th>\n",
       "      <th>Sex</th>\n",
       "      <th>Age</th>\n",
       "      <th>SibSp</th>\n",
       "      <th>Parch</th>\n",
       "      <th>Ticket</th>\n",
       "      <th>Fare</th>\n",
       "      <th>Cabin</th>\n",
       "      <th>Embarked</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Braund, Mr. Owen Harris</td>\n",
       "      <td>male</td>\n",
       "      <td>22.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>A/5 21171</td>\n",
       "      <td>7.2500</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>\n",
       "      <td>female</td>\n",
       "      <td>38.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>PC 17599</td>\n",
       "      <td>71.2833</td>\n",
       "      <td>C85</td>\n",
       "      <td>C</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>Heikkinen, Miss. Laina</td>\n",
       "      <td>female</td>\n",
       "      <td>26.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>STON/O2. 3101282</td>\n",
       "      <td>7.9250</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>4</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>\n",
       "      <td>female</td>\n",
       "      <td>35.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>113803</td>\n",
       "      <td>53.1000</td>\n",
       "      <td>C123</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>5</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Allen, Mr. William Henry</td>\n",
       "      <td>male</td>\n",
       "      <td>35.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>373450</td>\n",
       "      <td>8.0500</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   PassengerId  Survived  Pclass  \\\n",
       "0            1         0       3   \n",
       "1            2         1       1   \n",
       "2            3         1       3   \n",
       "3            4         1       1   \n",
       "4            5         0       3   \n",
       "\n",
       "                                                Name     Sex   Age  SibSp  \\\n",
       "0                            Braund, Mr. Owen Harris    male  22.0      1   \n",
       "1  Cumings, Mrs. John Bradley (Florence Briggs Th...  female  38.0      1   \n",
       "2                             Heikkinen, Miss. Laina  female  26.0      0   \n",
       "3       Futrelle, Mrs. Jacques Heath (Lily May Peel)  female  35.0      1   \n",
       "4                           Allen, Mr. William Henry    male  35.0      0   \n",
       "\n",
       "   Parch            Ticket     Fare Cabin Embarked  \n",
       "0      0         A/5 21171   7.2500   NaN        S  \n",
       "1      0          PC 17599  71.2833   C85        C  \n",
       "2      0  STON/O2. 3101282   7.9250   NaN        S  \n",
       "3      0            113803  53.1000  C123        S  \n",
       "4      0            373450   8.0500   NaN        S  "
      ]
     },
     "execution_count": 2,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "id": "cc1a3737",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:39.357845Z",
     "iopub.status.busy": "2022-04-11T12:52:39.357135Z",
     "iopub.status.idle": "2022-04-11T12:52:40.548376Z",
     "shell.execute_reply": "2022-04-11T12:52:40.547799Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.120021Z"
    },
    "papermill": {
     "duration": 1.219777,
     "end_time": "2022-04-11T12:52:40.548540",
     "exception": false,
     "start_time": "2022-04-11T12:52:39.328763",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "import matplotlib.pyplot as plt\n",
    "%matplotlib inline\n",
    "import seaborn as sns\n",
    "sns.set()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "id": "67c88e7f",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:40.612124Z",
     "iopub.status.busy": "2022-04-11T12:52:40.611445Z",
     "iopub.status.idle": "2022-04-11T12:52:40.971127Z",
     "shell.execute_reply": "2022-04-11T12:52:40.970436Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.134920Z"
    },
    "papermill": {
     "duration": 0.397556,
     "end_time": "2022-04-11T12:52:40.971289",
     "exception": false,
     "start_time": "2022-04-11T12:52:40.573733",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 1296x576 with 2 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "import warnings\n",
    "warnings.filterwarnings('ignore')\n",
    "f, ax = plt.subplots(1, 2, figsize=(18,8))\n",
    "train['Survived'].value_counts().plot.pie(explode=[0,0.1],autopct='%1.1f%%',ax=ax[0],shadow=True)\n",
    "ax[0].set_title('Survived')\n",
    "ax[0].set_ylabel('')\n",
    "sns.countplot('Survived',data=train,ax=ax[1])\n",
    "ax[1].set_title('Survived')\n",
    "plt.show()\n",
    "# 탑승객의 60%가 사망한것을 확인할 수 있음(Survived의 0 : 사망/ 1 : 생존)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "id": "052dfc19",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:41.029467Z",
     "iopub.status.busy": "2022-04-11T12:52:41.028732Z",
     "iopub.status.idle": "2022-04-11T12:52:41.324451Z",
     "shell.execute_reply": "2022-04-11T12:52:41.323916Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.454067Z"
    },
    "papermill": {
     "duration": 0.325986,
     "end_time": "2022-04-11T12:52:41.324600",
     "exception": false,
     "start_time": "2022-04-11T12:52:40.998614",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAA/YAAAHRCAYAAADALiLzAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjUuMSwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy/YYfK9AAAACXBIWXMAAAsTAAALEwEAmpwYAAC8dElEQVR4nOzdd3hcV50+8PfeudNHvRfbkuXei9xrHCcusdN7AoQAm8BmgV1ggV36QiAJu8CPtmRpu5ACJKTYsdPce2+SJVu99y5NuzP3/v6QM9JYtiXbmrlT3s/z5Il1NZK+ctE97z3nfI+gqqoKIiIiIiIiIgpLotYFEBEREREREdGNY7AnIiIiIiIiCmMM9kRERERERERhjMGeiIiIiIiIKIwx2BMRERERERGFMQZ7IiIiIiIiojDGYE+kgW9961v45S9/Oeqf9+c//zm+/OUvX/X9brcbGzduRHNz86h/7b///e945JFHRvTaH/3oR3j55ZdHvQYiIiIaKlTGHU6nE08//TTmz5+Pz3/+86Nez7V87GMfw9/+9rdhX+d2u7F+/Xq0t7cHoSqi0SNpXQBRqDh+/Dh+/OMfo6SkBDqdDuPHj8e//du/YdasWaP+tb73ve+N+uccib/85S/Iz89HamqqJl//I08++SQeeOAB3H///TAYDJrWQkREpIVoHHe8++67aG1txZEjRyBJoRlDDAYD7rvvPrz44ov42te+pnU5RCPGGXsiAL29vXj66afx+OOP4+jRo9i7dy+eeeaZGwqdqqpCUZQAVHnzXn31Vdx1111al4HU1FSMHz8eO3fu1LoUIiKioIvWcUd9fT1ycnJCNtR/ZPPmzXjjjTfgdru1LoVoxBjsiQBUVFQAADZt2gSdTgeTyYTly5djypQpAIYuNautrcXkyZPh8XgA9C/v+slPfoKHH34Ys2fPxm9/+1vce++9fl/jj3/8I55++mkAwNe+9jX85Cc/AQBs2LABu3bt8r3O4/Fg8eLFKCwsBACcPn0aDz/8MPLz83HnnXfiyJEjvtfW1NTg8ccfx9y5c/HJT34SHR0dV/0e6+vrUVNTg9mzZ/uufe1rX8N3vvMdfPrTn8bcuXPx8MMPo6WlBT/4wQ+wYMECrF+/HufPn/e9/sUXX8TatWsxd+5cbNy4ER988MFVv15ZWRk++clPYuHChVi3bh22bdvm9/6FCxdiz549V/14IiKiSBWN447/9//+H371q19h+/btmDt3rm9Z/GuvvYYNGzZgwYIF+NSnPoW6ujrf55g8eTJeeukl3H777Zg7dy5++tOforq6Gg8//DDmzZuHL3zhC77w3dXVhaeeegqLFy/GggUL8NRTT6GxsfGq9V3r66anpyMuLg6nT5++6scThRoGeyIAubm50Ol0+OpXv4o9e/agq6vruj/HW2+9hf/4j//AyZMn8cgjj6CiogKVlZW+92/ZsgWbN28e8nF33HEHtm7d6nt7//79SEhIwPTp09HU1ISnnnoKn/3sZ3H06FF89atfxec//3nfvq8vf/nLmD59Oo4cOYLPfe5zeOONN65a38WLFzFmzJghT8m3b9+OL37xizh8+DAMBgMeeughTJ8+HYcPH8a6devwwx/+0PfaMWPG4KWXXsKJEyfwzDPP4Ctf+coV9+vb7XY8+eST2LRpEw4ePIif/OQn+O53v4vS0lLfa/Ly8lBcXDz8bywREVGEicZxx+c//3k89dRT2LBhA06dOoUHHngAH374IX7zm9/gF7/4BQ4dOoT58+fjS1/6kt/n2b9/P/7+97/jr3/9K37729/im9/8Jl544QXs2bMHJSUleOeddwAAiqLg3nvvxa5du7Br1y4YjcarbkEYydcdP348xykUVhjsiQDYbDa8/PLLEAQB3/zmN7FkyRI8/fTTaG1tHfHnuOeeezBx4kRIkoSYmBjceuutvhtnZWUlysvLsWbNmiEft3nzZuzcuRMOhwNA/434jjvuANB/0165ciVWrVoFURSxbNkyzJgxA3v27EF9fT3OnTuHL3zhCzAYDFiwYMEVP/9Huru7YbVah1y/7bbbMGPGDBiNRtx2220wGo24++67odPpsHHjRhQVFfleu2HDBqSlpUEURWzcuBHjxo3D2bNnh3zO3bt3IysrC/fddx8kScK0adOwbt06vPvuu77XWK1WdHd3j/B3l4iIKHJE87hjsFdffRX/8A//gLy8PEiShKeffhpFRUV+s+ef/vSnYbPZMHHiREyaNAnLli3DmDFjEBMTg5UrV/pWFiYkJGDdunUwm82w2Wz47Gc/i2PHjt3w1+U4hcINgz3RJXl5efjRj36EvXv3YsuWLWhubsazzz474o/PyMjwe3vz5s2+p8hbt27F2rVrYTabh3zcuHHjkJeXh127dsHhcGDnzp2+J+z19fV49913kZ+f7/vvxIkTaGlpQXNzM2JjY2GxWHyfKzMz86r1xcXFoa+vb8j1pKQk369NJhOSk5P93rbb7b6333zzTdx1112+WkpKSq64DK+urg5nz571q3vLli1oaWnxvaavrw+xsbFXrZeIiCiSReu4Y7D6+no8++yzvq+1cOFCqKqKpqYm32sGj0uMRuOQtz8apzgcDnzrW9/CLbfcgnnz5uGxxx5Dd3c3vF7vDX1djlMo3IR25woijeTl5eHee+/FX/7yFwCA2WyG0+n0vf9KT9QFQfB7e+nSpWhvb0dRURG2bt2Kr3/961f9eps2bcLWrVuhKAomTJiAcePGAei/ad911134/ve/P+Rj6urq0N3dDbvd7rvJ1tfXD6njI5MnT0ZtbS08Hs8NNa2pq6vDN77xDfzxj3/E3LlzodPprtqILyMjAwsWLMAf/vCHq36+srIy315CIiKiaBat446MjAw8/fTTuPPOO69a60j9/ve/R0VFBf76178iJSUFRUVFuPvuu6Gq6g193fLycjz55JM3XRdRsHDGngj9IfP3v/+9r8lKQ0MDtm7d6mv4MnXqVBw7dgz19fXo6enBb37zm2E/p16vx/r16/H888+jq6sLy5Ytu+prN27ciAMHDuCVV17Bpk2bfNfvvPNO7Nq1C/v27YPX64XL5cKRI0fQ2NiIrKwszJgxAz//+c/hdrtx/Phxv2Y4l0tPT8fYsWOvuHR+JBwOBwRBQGJiIgDg9ddfR0lJyRVfu3r1alRWVuLNN9+ELMuQZRlnz55FWVmZ7zXHjh3DihUrbqgWIiKicMZxR7+HH34YL774om880dPTg+3btw/7vV5JX18fjEYjYmNj0dnZiV/84hc3/HWbmprQ1dWFOXPm3FAtRFpgsCdC/163M2fO4IEHHsCcOXPw4IMPYtKkSb7zS5ctW4aNGzfizjvvxL333otbbrllRJ938+bNOHjwINavX3/NWfLU1FTMmTMHp06dwsaNG33XMzIy8Ktf/Qq/+c1vsGTJEqxatQq/+93vfMfa/Od//ifOnDmDRYsW4Ze//CXuvvvua9bz8MMP46233hpR7ZebMGECnnzySTz88MNYunQpLl68iHnz5l3xtTabDb/73e+wbds2rFixAsuXL8ePf/xjX+fa5uZmlJaWYu3atTdUCxERUTjjuKPfbbfdhk9/+tP4l3/5F8ybNw+bNm3C3r17R/S9Xu4Tn/gEXC4XFi9ejIceeuiakwfDfd0tW7bg7rvvvqHjB4m0IqhXWp9CRBHJ7Xbj7rvvxh//+EekpqZqVsePfvQjjBkzBo899phmNRAREVFghcq443q43W7ceeedeOmll/z6EBGFOgZ7IiIiIiIiojDGpfhEREREREREYYzBnoiIiIiIiCiMMdgTERERERERhTEGeyIiIiIiIqIwxmBPREREREREFMYY7ImIiIiIiIjCGIM9ERERERERURhjsCciIiIiIiIKYwz2RERERERERGGMwZ6IiIiIiIgojDHYExEREREREYUxBnsiIiIiIiKiMMZgT0RERERERBTGGOyJiIiIiIiIwhiDPREREREREVEYY7AnIiIiIiIiCmMM9kRERERERERhjMGeiIiIiIiIKIwx2BMRERERERGFMQZ7IiIiIiIiojDGYE9EREREREQUxhjsiYiIiIiIiMIYgz0RERERERFRGGOwJyIiIiIiIgpjDPZEREREREREYYzBnoiIiIiIiCiMMdgTERERERERhTEGeyIiIiIiIqIwxmBPREREREREFMYY7ImIiIiIiIjCGIM9ERERERERURhjsCciIiIiIiIKYwz2RERERERERGGMwZ6IiIiIiIgojDHYExEREREREYUxBnsiIiIiIiKiMMZgT0RERERERBTGGOyJiIiIiIiIwhiDPREREREREVEYY7AnIiIiIiIiCmMM9kRERERERERhjMGeiIiIiIiIKIwx2BMRERERERGFMQZ7IiIiIiIiojDGYE9EREREREQUxhjsiYiIiIiIiMIYgz0RERERERFRGJO0LoCIBrhlL2SvAlVVIQoCBEGAIAA6UYAoChAFAV5FhaKoUFVAVVWouPT/S2/rJRF6vQ6KosLjVXyvBQBBAERRgE4UoZf4XI+IiIj8KaoKt+yF16tChQoBAoD+MYQgDIxHdGL/GMXjVeH1KvB4FagqIEn9Ywyd2D9mGRi3XBqrAP2fUQBEQYBeJ0LimITopjHYEwWBx6PA7fFCUQFRAAySDipU9PTJ6OhxoqXTgcY2O1o67GjvdqK924lehwy37IVbVuCSvf2h36OM+GuaDDpYTHpYzXpYTBKsJj0s5kv/N0mwmvWIsxoRYzUg1mpAaoIFibEmKKoKj0eBKAowGnQQBSGAvzNEREQU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\n",
      "text/plain": [
       "<Figure size 1296x576 with 2 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "# 남녀 생존 비율 -> 여성보다 남성의 사망률이 더 크다\n",
    "f,ax=plt.subplots(1,2,figsize=(18,8))\n",
    "train['Survived'][train['Sex']=='male'].value_counts().plot.pie(explode=[0,0.1],autopct='%1.1f%%',ax=ax[0],shadow=True)\n",
    "train['Survived'][train['Sex']=='female'].value_counts().plot.pie(explode=[0,0.1],autopct='%1.1f%%',ax=ax[1],shadow=True)\n",
    "ax[0].set_title('Survived (male)')\n",
    "ax[1].set_title('Survived (female)')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "id": "a9158ee3",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:41.389146Z",
     "iopub.status.busy": "2022-04-11T12:52:41.388516Z",
     "iopub.status.idle": "2022-04-11T12:52:41.435852Z",
     "shell.execute_reply": "2022-04-11T12:52:41.435239Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.767153Z"
    },
    "papermill": {
     "duration": 0.082544,
     "end_time": "2022-04-11T12:52:41.435999",
     "exception": false,
     "start_time": "2022-04-11T12:52:41.353455",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Pclass</th>\n",
       "      <th>1</th>\n",
       "      <th>2</th>\n",
       "      <th>3</th>\n",
       "      <th>All</th>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Sex</th>\n",
       "      <th>Survived</th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th rowspan=\"2\" valign=\"top\">female</th>\n",
       "      <th>0</th>\n",
       "      <td>3</td>\n",
       "      <td>6</td>\n",
       "      <td>72</td>\n",
       "      <td>81</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>91</td>\n",
       "      <td>70</td>\n",
       "      <td>72</td>\n",
       "      <td>233</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th rowspan=\"2\" valign=\"top\">male</th>\n",
       "      <th>0</th>\n",
       "      <td>77</td>\n",
       "      <td>91</td>\n",
       "      <td>300</td>\n",
       "      <td>468</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>45</td>\n",
       "      <td>17</td>\n",
       "      <td>47</td>\n",
       "      <td>109</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>All</th>\n",
       "      <th></th>\n",
       "      <td>216</td>\n",
       "      <td>184</td>\n",
       "      <td>491</td>\n",
       "      <td>891</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "Pclass             1    2    3  All\n",
       "Sex    Survived                    \n",
       "female 0           3    6   72   81\n",
       "       1          91   70   72  233\n",
       "male   0          77   91  300  468\n",
       "       1          45   17   47  109\n",
       "All              216  184  491  891"
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Pclass 등급에 따른 생존률 -> Pclass가 좋을수록 사망률이 낮다\n",
    "pd.crosstab([train['Sex'],train['Survived']],train['Pclass'],margins=True)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "id": "566f112c",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:41.498921Z",
     "iopub.status.busy": "2022-04-11T12:52:41.497891Z",
     "iopub.status.idle": "2022-04-11T12:52:41.539269Z",
     "shell.execute_reply": "2022-04-11T12:52:41.539794Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.815294Z"
    },
    "papermill": {
     "duration": 0.074099,
     "end_time": "2022-04-11T12:52:41.539972",
     "exception": false,
     "start_time": "2022-04-11T12:52:41.465873",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Embarked</th>\n",
       "      <th>C</th>\n",
       "      <th>Q</th>\n",
       "      <th>S</th>\n",
       "      <th>All</th>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Sex</th>\n",
       "      <th>Survived</th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th rowspan=\"2\" valign=\"top\">female</th>\n",
       "      <th>0</th>\n",
       "      <td>9</td>\n",
       "      <td>9</td>\n",
       "      <td>63</td>\n",
       "      <td>81</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>64</td>\n",
       "      <td>27</td>\n",
       "      <td>140</td>\n",
       "      <td>231</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th rowspan=\"2\" valign=\"top\">male</th>\n",
       "      <th>0</th>\n",
       "      <td>66</td>\n",
       "      <td>38</td>\n",
       "      <td>364</td>\n",
       "      <td>468</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>29</td>\n",
       "      <td>3</td>\n",
       "      <td>77</td>\n",
       "      <td>109</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>All</th>\n",
       "      <th></th>\n",
       "      <td>168</td>\n",
       "      <td>77</td>\n",
       "      <td>644</td>\n",
       "      <td>889</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "Embarked           C   Q    S  All\n",
       "Sex    Survived                   \n",
       "female 0           9   9   63   81\n",
       "       1          64  27  140  231\n",
       "male   0          66  38  364  468\n",
       "       1          29   3   77  109\n",
       "All              168  77  644  889"
      ]
     },
     "execution_count": 7,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# (C = Cherbourg, Q = Queenstown, S = Southampton)\n",
    "pd.crosstab([train['Sex'],train['Survived']],train['Embarked'],margins=True)\n",
    "# Southampton에서 탑승한 사람이 많았고, 생존자보다 사망자의 비율이 컸다.\n",
    "# Cherbourg에서 탑승한 사람 중에서는 생존한 사람의 비율이 가장 많았다.\n",
    "# Queenstown에서 탑승한 사람도 생존자보다 사망자의 비율이 컸다."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "7d30f63e",
   "metadata": {
    "papermill": {
     "duration": 0.029222,
     "end_time": "2022-04-11T12:52:41.599230",
     "exception": false,
     "start_time": "2022-04-11T12:52:41.570008",
     "status": "completed"
    },
    "tags": []
   },
   "source": [
    "## 전처리(pre-processing)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "id": "636b759a",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:41.662917Z",
     "iopub.status.busy": "2022-04-11T12:52:41.662288Z",
     "iopub.status.idle": "2022-04-11T12:52:41.669856Z",
     "shell.execute_reply": "2022-04-11T12:52:41.670448Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.863628Z"
    },
    "papermill": {
     "duration": 0.04017,
     "end_time": "2022-04-11T12:52:41.670619",
     "exception": false,
     "start_time": "2022-04-11T12:52:41.630449",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "PassengerId      0\n",
       "Survived         0\n",
       "Pclass           0\n",
       "Name             0\n",
       "Sex              0\n",
       "Age            177\n",
       "SibSp            0\n",
       "Parch            0\n",
       "Ticket           0\n",
       "Fare             0\n",
       "Cabin          687\n",
       "Embarked         2\n",
       "dtype: int64"
      ]
     },
     "execution_count": 8,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train.isnull().sum()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "56ed7785",
   "metadata": {
    "papermill": {
     "duration": 0.029828,
     "end_time": "2022-04-11T12:52:41.730654",
     "exception": false,
     "start_time": "2022-04-11T12:52:41.700826",
     "status": "completed"
    },
    "tags": []
   },
   "source": [
    "`Age`, `Cabin`, `Embarked`에 빈 값이 있는것을 확인할 수 있다.\n",
    "\n",
    "이러한 **결측값**을 처리하는 전처리 과정이 필요하다."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "id": "2cb4ad9b",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:41.794476Z",
     "iopub.status.busy": "2022-04-11T12:52:41.793805Z",
     "iopub.status.idle": "2022-04-11T12:52:41.798249Z",
     "shell.execute_reply": "2022-04-11T12:52:41.798787Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.876970Z"
    },
    "papermill": {
     "duration": 0.038212,
     "end_time": "2022-04-11T12:52:41.798964",
     "exception": false,
     "start_time": "2022-04-11T12:52:41.760752",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "# 'Embarked'의 누락값을 제일 많이 탑승한 항구인 'Southampton'에서 탔다고 가정하여 누락값을 채운다\n",
    "train['Embarked'].fillna('S',inplace=True) "
   ]
  },
  {
   "cell_type": "markdown",
   "id": "7b63fc2d",
   "metadata": {
    "papermill": {
     "duration": 0.030067,
     "end_time": "2022-04-11T12:52:41.860900",
     "exception": false,
     "start_time": "2022-04-11T12:52:41.830833",
     "status": "completed"
    },
    "tags": []
   },
   "source": [
    "## 예측 모델 생성 및 결과"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "id": "516bcb9b",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:41.925093Z",
     "iopub.status.busy": "2022-04-11T12:52:41.924433Z",
     "iopub.status.idle": "2022-04-11T12:52:42.382628Z",
     "shell.execute_reply": "2022-04-11T12:52:42.381884Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.885752Z"
    },
    "papermill": {
     "duration": 0.491179,
     "end_time": "2022-04-11T12:52:42.382774",
     "exception": false,
     "start_time": "2022-04-11T12:52:41.891595",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "from sklearn.linear_model import LogisticRegression\n",
    "from sklearn.svm import SVC\n",
    "from sklearn.neighbors import KNeighborsClassifier\n",
    "from sklearn.ensemble import RandomForestClassifier\n",
    "from sklearn.naive_bayes import GaussianNB\n",
    "from sklearn.utils import shuffle"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "id": "67601a4f",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:42.448594Z",
     "iopub.status.busy": "2022-04-11T12:52:42.447559Z",
     "iopub.status.idle": "2022-04-11T12:52:42.460454Z",
     "shell.execute_reply": "2022-04-11T12:52:42.460997Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.897001Z"
    },
    "papermill": {
     "duration": 0.047545,
     "end_time": "2022-04-11T12:52:42.461178",
     "exception": false,
     "start_time": "2022-04-11T12:52:42.413633",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "# 수치 속성을 위한 파이프라인부터 시작하여 전처리 파이프라인을 구축\n",
    "from sklearn.pipeline import Pipeline\n",
    "from sklearn.impute import SimpleImputer\n",
    "from sklearn.preprocessing import StandardScaler\n",
    "\n",
    "num_pipeline = Pipeline([\n",
    "        (\"imputer\", SimpleImputer(strategy=\"median\")),\n",
    "        (\"scaler\", StandardScaler())\n",
    "    ])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "id": "851c3e32",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:42.526745Z",
     "iopub.status.busy": "2022-04-11T12:52:42.525696Z",
     "iopub.status.idle": "2022-04-11T12:52:42.531205Z",
     "shell.execute_reply": "2022-04-11T12:52:42.531799Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.915599Z"
    },
    "papermill": {
     "duration": 0.040049,
     "end_time": "2022-04-11T12:52:42.531995",
     "exception": false,
     "start_time": "2022-04-11T12:52:42.491946",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "# 범주 속성을 위한 파이프라인을 구축\n",
    "from sklearn.preprocessing import OrdinalEncoder, OneHotEncoder\n",
    "cat_pipeline = Pipeline([\n",
    "        (\"ordinal_encoder\", OrdinalEncoder()),    \n",
    "        (\"imputer\", SimpleImputer(strategy=\"most_frequent\")),\n",
    "        (\"cat_encoder\", OneHotEncoder(sparse=False)),\n",
    "    ])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "id": "57ede569",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:42.596902Z",
     "iopub.status.busy": "2022-04-11T12:52:42.595860Z",
     "iopub.status.idle": "2022-04-11T12:52:42.608601Z",
     "shell.execute_reply": "2022-04-11T12:52:42.609156Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.923290Z"
    },
    "papermill": {
     "duration": 0.046851,
     "end_time": "2022-04-11T12:52:42.609361",
     "exception": false,
     "start_time": "2022-04-11T12:52:42.562510",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "# 수치 및 범주형 파이프라인\n",
    "from sklearn.compose import ColumnTransformer\n",
    "\n",
    "num_attribs = [\"Age\", \"SibSp\", \"Parch\", \"Fare\"]\n",
    "cat_attribs = [\"Pclass\", \"Sex\", \"Embarked\"]\n",
    "\n",
    "preprocess_pipeline = ColumnTransformer([\n",
    "        (\"num\", num_pipeline, num_attribs),\n",
    "        (\"cat\", cat_pipeline, cat_attribs),\n",
    "    ])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "id": "f132893d",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:42.675004Z",
     "iopub.status.busy": "2022-04-11T12:52:42.673853Z",
     "iopub.status.idle": "2022-04-11T12:52:42.693813Z",
     "shell.execute_reply": "2022-04-11T12:52:42.694424Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.941164Z"
    },
    "papermill": {
     "duration": 0.054112,
     "end_time": "2022-04-11T12:52:42.694610",
     "exception": false,
     "start_time": "2022-04-11T12:52:42.640498",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([[-0.56573646,  0.43279337, -0.47367361, ...,  0.        ,\n",
       "         0.        ,  1.        ],\n",
       "       [ 0.66386103,  0.43279337, -0.47367361, ...,  1.        ,\n",
       "         0.        ,  0.        ],\n",
       "       [-0.25833709, -0.4745452 , -0.47367361, ...,  0.        ,\n",
       "         0.        ,  1.        ],\n",
       "       ...,\n",
       "       [-0.1046374 ,  0.43279337,  2.00893337, ...,  0.        ,\n",
       "         0.        ,  1.        ],\n",
       "       [-0.25833709, -0.4745452 , -0.47367361, ...,  1.        ,\n",
       "         0.        ,  0.        ],\n",
       "       [ 0.20276197, -0.4745452 , -0.47367361, ...,  0.        ,\n",
       "         1.        ,  0.        ]])"
      ]
     },
     "execution_count": 14,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "X_train = preprocess_pipeline.fit_transform(train)\n",
    "X_train"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "id": "2c63c5b0",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:42.764288Z",
     "iopub.status.busy": "2022-04-11T12:52:42.763280Z",
     "iopub.status.idle": "2022-04-11T12:52:42.768505Z",
     "shell.execute_reply": "2022-04-11T12:52:42.769084Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.964920Z"
    },
    "papermill": {
     "duration": 0.042347,
     "end_time": "2022-04-11T12:52:42.769253",
     "exception": false,
     "start_time": "2022-04-11T12:52:42.726906",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0      0\n",
       "1      1\n",
       "2      1\n",
       "3      1\n",
       "4      0\n",
       "      ..\n",
       "886    0\n",
       "887    1\n",
       "888    0\n",
       "889    1\n",
       "890    0\n",
       "Name: Survived, Length: 891, dtype: int64"
      ]
     },
     "execution_count": 15,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "y_train = train[\"Survived\"]\n",
    "y_train"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "id": "4e7aebb1",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:42.835862Z",
     "iopub.status.busy": "2022-04-11T12:52:42.834791Z",
     "iopub.status.idle": "2022-04-11T12:52:43.062875Z",
     "shell.execute_reply": "2022-04-11T12:52:43.063471Z",
     "shell.execute_reply.started": "2022-04-11T12:52:03.975626Z"
    },
    "papermill": {
     "duration": 0.263164,
     "end_time": "2022-04-11T12:52:43.063650",
     "exception": false,
     "start_time": "2022-04-11T12:52:42.800486",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "RandomForestClassifier(random_state=42)"
      ]
     },
     "execution_count": 16,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# RandomForestClassifer로 훈련\n",
    "from sklearn.ensemble import RandomForestClassifier\n",
    "forest_clf = RandomForestClassifier(n_estimators=100, random_state=42)\n",
    "forest_clf.fit(X_train, y_train)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "id": "31f983d8",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:43.131649Z",
     "iopub.status.busy": "2022-04-11T12:52:43.130965Z",
     "iopub.status.idle": "2022-04-11T12:52:43.161954Z",
     "shell.execute_reply": "2022-04-11T12:52:43.162483Z",
     "shell.execute_reply.started": "2022-04-11T12:52:04.206242Z"
    },
    "papermill": {
     "duration": 0.067194,
     "end_time": "2022-04-11T12:52:43.162683",
     "exception": false,
     "start_time": "2022-04-11T12:52:43.095489",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "# 교육받은 모델로 테스트 셋에 대한 예측\n",
    "X_test = preprocess_pipeline.transform(test)\n",
    "y_pred = forest_clf.predict(X_test)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "id": "f34f6d47",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:43.230023Z",
     "iopub.status.busy": "2022-04-11T12:52:43.229282Z",
     "iopub.status.idle": "2022-04-11T12:52:45.440957Z",
     "shell.execute_reply": "2022-04-11T12:52:45.441515Z",
     "shell.execute_reply.started": "2022-04-11T12:52:04.241980Z"
    },
    "papermill": {
     "duration": 2.247179,
     "end_time": "2022-04-11T12:52:45.441686",
     "exception": false,
     "start_time": "2022-04-11T12:52:43.194507",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.8092759051186016"
      ]
     },
     "execution_count": 18,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "from sklearn.model_selection import cross_val_score\n",
    "forest_scores = cross_val_score(forest_clf, X_train, y_train, cv=10)\n",
    "forest_scores.mean()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 19,
   "id": "72dfcd9c",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:45.509977Z",
     "iopub.status.busy": "2022-04-11T12:52:45.509185Z",
     "iopub.status.idle": "2022-04-11T12:52:45.772646Z",
     "shell.execute_reply": "2022-04-11T12:52:45.772060Z",
     "shell.execute_reply.started": "2022-04-11T12:52:06.510597Z"
    },
    "papermill": {
     "duration": 0.298582,
     "end_time": "2022-04-11T12:52:45.772804",
     "exception": false,
     "start_time": "2022-04-11T12:52:45.474222",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.8249313358302123"
      ]
     },
     "execution_count": 19,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# SVC로 시도(RandomForestClassifer보다 더 성능이 좋음을 확인할 수 있다)\n",
    "from sklearn.svm import SVC\n",
    "svm_clf = SVC(gamma=\"auto\")\n",
    "svm_scores = cross_val_score(svm_clf, X_train, y_train, cv=10)\n",
    "svm_scores.mean()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 20,
   "id": "935b74ee",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:45.844963Z",
     "iopub.status.busy": "2022-04-11T12:52:45.844127Z",
     "iopub.status.idle": "2022-04-11T12:52:45.889983Z",
     "shell.execute_reply": "2022-04-11T12:52:45.889254Z",
     "shell.execute_reply.started": "2022-04-11T12:52:06.775803Z"
    },
    "papermill": {
     "duration": 0.084727,
     "end_time": "2022-04-11T12:52:45.890128",
     "exception": false,
     "start_time": "2022-04-11T12:52:45.805401",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "# SVC로 테스트 셋에 대한 예측\n",
    "svm_clf.fit(X_train, y_train)\n",
    "X_test = preprocess_pipeline.transform(test)\n",
    "y_pred = svm_clf.predict(X_test)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 21,
   "id": "9aed5f85",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2022-04-11T12:52:45.961874Z",
     "iopub.status.busy": "2022-04-11T12:52:45.961100Z",
     "iopub.status.idle": "2022-04-11T12:52:45.975015Z",
     "shell.execute_reply": "2022-04-11T12:52:45.974444Z",
     "shell.execute_reply.started": "2022-04-11T12:52:06.827112Z"
    },
    "papermill": {
     "duration": 0.05224,
     "end_time": "2022-04-11T12:52:45.975156",
     "exception": false,
     "start_time": "2022-04-11T12:52:45.922916",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>PassengerId</th>\n",
       "      <th>Survived</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>892</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>893</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>894</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>895</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>896</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>...</th>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>413</th>\n",
       "      <td>1305</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>414</th>\n",
       "      <td>1306</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>415</th>\n",
       "      <td>1307</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>416</th>\n",
       "      <td>1308</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>417</th>\n",
       "      <td>1309</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>418 rows × 2 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "     PassengerId  Survived\n",
       "0            892         0\n",
       "1            893         1\n",
       "2            894         0\n",
       "3            895         0\n",
       "4            896         1\n",
       "..           ...       ...\n",
       "413         1305         0\n",
       "414         1306         1\n",
       "415         1307         0\n",
       "416         1308         0\n",
       "417         1309         0\n",
       "\n",
       "[418 rows x 2 columns]"
      ]
     },
     "execution_count": 21,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "submission = pd.DataFrame({\"PassengerId\": test[\"PassengerId\"],\"Survived\": y_pred })\n",
    "\n",
    "submission.to_csv('submission.csv', index=False)\n",
    "submission"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.7.12"
  },
  "papermill": {
   "default_parameters": {},
   "duration": 18.09453,
   "end_time": "2022-04-11T12:52:46.720075",
   "environment_variables": {},
   "exception": null,
   "input_path": "__notebook__.ipynb",
   "output_path": "__notebook__.ipynb",
   "parameters": {},
   "start_time": "2022-04-11T12:52:28.625545",
   "version": "2.3.3"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}

