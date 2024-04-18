import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
import re

# Function to alter timestamp column and rename specific columns
def preprocess_data(df):
    # Alter timestamp column to extract time
    df['timestamp'] = pd.to_datetime(df['timestamp'])
    df['Time'] = (df['timestamp'] - df['timestamp'].iloc[0]).dt.total_seconds()
    
    # Rename specific columns
    column_mapping = {
        'TL28_DD::Erd_Drive_SpeedSensorBasket': 'Basket Speed',
        'TL28_DD::Erd_AccelerometerVirtualPoint1XDisplacement': 'VP1X',
        'TL28_DD::Erd_AccelerometerVirtualPoint1YDisplacement': 'VP1Y',
        'TL28_DD::Erd_AccelerometerVirtualPoint1ZDisplacement': 'VP1Z',
        'TL28_DD::Erd_AccelerometerVirtualPoint2XDisplacement': 'VP2X',
        'TL28_DD::Erd_AccelerometerVirtualPoint2YDisplacement': 'VP2Y',
        'TL28_DD::Erd_AccelerometerVirtualPoint2ZDisplacement': 'VP2Z',
        'TL28_DD::Erd_AccelerometerVirtualPoint3XDisplacement': 'VP3X',
        'TL28_DD::Erd_AccelerometerVirtualPoint3YDisplacement': 'VP3Y',
        'TL28_DD::Erd_AccelerometerVirtualPoint3ZDisplacement': 'VP3Z',
        'TL28_DD::Erd_AccelerometerReportedRotationAmplitude': 'Wobble Angle'
    }
    df.rename(columns=column_mapping, inplace=True)
    
    return df

# Streamlit application
def main():
    st.title("Applcore Data Analysis")
    
    #Defining the colors for each label, used further down the script
    color_dict = {"P1":"C0", "P2":"C1", "P3": "C2", "P4":"C3", "P5": "C4", "P6": "C5", "P7":"C6", "P8":"C7", "P9":"C9"}
    
    
   
    
    # File upload and preprocessing
    dfs = []
    file_labels = []
    file_colors = []
    #accept multiple files allows for the fle uploader to take in as many csv files as needed
    uploaded_files = st.sidebar.file_uploader("Upload CSV files", type="csv", accept_multiple_files=True)
    
    for i, file in enumerate(uploaded_files):
        if file is not None:
            df = pd.read_csv(file)
            df = preprocess_data(df)
            dfs.append(df)
            
            # Extract label from file name using regular expressions
            label_match = re.search(r'P\d+', file.name)
            if label_match:
                label = label_match.group()
                file_labels.append(label)
                file_colors.append(color_dict.get(label, "black"))
            else:
                file_labels.append(f"File {i+1}")
    
   
        
    
    selected_column = st.sidebar.selectbox("Select a column", options=['VP1X', 'VP1Y', 'VP1Z', 'VP2X',
                                                                       'VP2Y', 'VP2Z', 'VP3X', 'VP3Y', 'VP3Z',
                                                                       'Wobble Angle'])
    title = st.sidebar.text_input('Enter a title for your chart')
    Scale = st.sidebar.number_input('Y axis Min')
    scale = st.sidebar.number_input('Y axis Max')
    Scalex = st.sidebar.number_input('X axis Min')
    scalex = st.sidebar.number_input('X axis Max')
    # Plotting
    if len(dfs) > 0:
        st.header("Data Visualization")
        
        fig, ax = plt.subplots()
        if st.checkbox('Basket Speed'):
            ax2 = ax.twinx()
            for i, df in enumerate(dfs):
                ax2.plot(df['Time'], df['Basket Speed'], label=file_labels[i], color = 'navy')
                ax2.set_ylabel('Basket Speed (RPM)')
                ax2.set_ylim(0,1000)
                break
            
        for i, df in enumerate(dfs):
            ax.plot(df['Time'], df[selected_column], label=file_labels[i], color = file_colors[i])
        
    
        ax.set_xlabel('Time (sec)')
        ax.set_ylabel('Displacement(in x 1000) - Wobble Angle (deg x 1000)')
        ax.set_xlim(Scalex, scalex)
        ax.set_ylim(Scale, scale)  
        ax.grid()
        lines, labels = ax.get_legend_handles_labels()
        ax.legend(lines, labels, loc='upper center', bbox_to_anchor=(0.5, -.11), fancybox=True, shadow=True, ncol=5)
        plt.title(title)
        plt.title(selected_column, loc = "right", fontsize = 8)
        st.pyplot(fig)

if __name__ == "__main__":
    main()
