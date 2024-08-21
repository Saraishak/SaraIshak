.. code:: ipython3

    !pip install pandas numpy scipy ipywidgets
    import pandas as pd
    import numpy as np
    import scipy.stats as stats
    import ipywidgets as widgets
    from IPython.display import display, HTML
    
    # Define the simulation function
    def run_simulation(arrival_rate, num_servers, departure_rate):
        # Initialize lists
        Arrival = [stats.expon(scale=arrival_rate).rvs()]
        Length_Of_Service = []
        Departure = []
        Time_Of_Service = []
        Length_Of_Wait = []
    
        # Generate arrival times
        for i in range(1, 100):
            next_arrival = Arrival[-1] + stats.expon(scale=arrival_rate).rvs()
            Arrival.append(next_arrival)
    
        # Process each customer
        for i in range(100):
            Length_Of_Service.append(stats.expon(scale=departure_rate).rvs())
    
            if i < num_servers:
                service_start_time = Arrival[i]
            else:
                if len(Departure) < num_servers:
                    service_start_time = Arrival[i]
                else:
                    Chrono_Departures = sorted(Departure)
                    service_start_time = max(Arrival[i], Chrono_Departures[-num_servers])
    
            Time_Of_Service.append(service_start_time)
            departure_time = service_start_time + Length_Of_Service[i]
            Departure.append(departure_time)
            wait_time = service_start_time - Arrival[i]
            Length_Of_Wait.append(wait_time)
    
        # Create DataFrame
        data = {
            'Arrival': Arrival,
            'Service time': Time_Of_Service,
            'Departure': Departure
        }
    
        df = pd.DataFrame(data)
    
        # Define a function for conditional formatting
        def highlight_service_time(val):
            if val in df['Arrival'].values:
                return 'color: red'
            else:
                return ''
    
        # Apply styling using apply
        styled_df = df.head(15).style.apply(lambda x: [highlight_service_time(val) for val in x], subset=['Service time'])
    
        # Convert the styled DataFrame to HTML
        html = styled_df.to_html()  # Use to_html() to get HTML representation
        
        return html
    
    # Define the sliders with updated labels
    arrival_rate_slider = widgets.IntSlider(value=3, min=1, max=10, step=1, description='Arrival Rate:')
    num_servers_slider = widgets.IntSlider(value=2, min=1, max=5, step=1, description='Number of Servers:')
    departure_rate_slider = widgets.IntSlider(value=8, min=1, max=60, step=1, description='Departure Rate:')
    
    # Define the interactive function
    def update_simulation(arrival_rate, num_servers, departure_rate):
        html = run_simulation(arrival_rate, num_servers, departure_rate)
        display(HTML(html))
    
    # Use interactive widgets with updated dictionary keys
    ui = widgets.VBox([arrival_rate_slider, num_servers_slider, departure_rate_slider])
    out = widgets.interactive_output(update_simulation, {
        'arrival_rate': arrival_rate_slider,
        'num_servers': num_servers_slider,
        'departure_rate': departure_rate_slider
    })
    
    display(ui, out)
    


.. parsed-literal::

    Requirement already satisfied: pandas in c:\users\sara ishak\anaconda3\lib\site-packages (2.2.1)
    Requirement already satisfied: numpy in c:\users\sara ishak\anaconda3\lib\site-packages (1.26.4)
    Requirement already satisfied: scipy in c:\users\sara ishak\anaconda3\lib\site-packages (1.13.1)
    Requirement already satisfied: ipywidgets in c:\users\sara ishak\anaconda3\lib\site-packages (8.1.3)
    Requirement already satisfied: python-dateutil>=2.8.2 in c:\users\sara ishak\anaconda3\lib\site-packages (from pandas) (2.8.2)
    Requirement already satisfied: pytz>=2020.1 in c:\users\sara ishak\anaconda3\lib\site-packages (from pandas) (2023.3.post1)
    Requirement already satisfied: tzdata>=2022.7 in c:\users\sara ishak\anaconda3\lib\site-packages (from pandas) (2023.3)
    Requirement already satisfied: comm>=0.1.3 in c:\users\sara ishak\anaconda3\lib\site-packages (from ipywidgets) (0.2.2)
    Requirement already satisfied: ipython>=6.1.0 in c:\users\sara ishak\anaconda3\lib\site-packages (from ipywidgets) (8.20.0)
    Requirement already satisfied: traitlets>=4.3.1 in c:\users\sara ishak\anaconda3\lib\site-packages (from ipywidgets) (5.7.1)
    Requirement already satisfied: widgetsnbextension~=4.0.11 in c:\users\sara ishak\anaconda3\lib\site-packages (from ipywidgets) (4.0.11)
    Requirement already satisfied: jupyterlab-widgets~=3.0.11 in c:\users\sara ishak\anaconda3\lib\site-packages (from ipywidgets) (3.0.11)
    Requirement already satisfied: decorator in c:\users\sara ishak\anaconda3\lib\site-packages (from ipython>=6.1.0->ipywidgets) (5.1.1)
    Requirement already satisfied: jedi>=0.16 in c:\users\sara ishak\anaconda3\lib\site-packages (from ipython>=6.1.0->ipywidgets) (0.18.1)
    Requirement already satisfied: matplotlib-inline in c:\users\sara ishak\anaconda3\lib\site-packages (from ipython>=6.1.0->ipywidgets) (0.1.6)
    Requirement already satisfied: prompt-toolkit<3.1.0,>=3.0.41 in c:\users\sara ishak\anaconda3\lib\site-packages (from ipython>=6.1.0->ipywidgets) (3.0.43)
    Requirement already satisfied: pygments>=2.4.0 in c:\users\sara ishak\anaconda3\lib\site-packages (from ipython>=6.1.0->ipywidgets) (2.15.1)
    Requirement already satisfied: stack-data in c:\users\sara ishak\anaconda3\lib\site-packages (from ipython>=6.1.0->ipywidgets) (0.2.0)
    Requirement already satisfied: colorama in c:\users\sara ishak\anaconda3\lib\site-packages (from ipython>=6.1.0->ipywidgets) (0.4.6)
    Requirement already satisfied: six>=1.5 in c:\users\sara ishak\anaconda3\lib\site-packages (from python-dateutil>=2.8.2->pandas) (1.16.0)
    Requirement already satisfied: parso<0.9.0,>=0.8.0 in c:\users\sara ishak\anaconda3\lib\site-packages (from jedi>=0.16->ipython>=6.1.0->ipywidgets) (0.8.3)
    Requirement already satisfied: wcwidth in c:\users\sara ishak\anaconda3\lib\site-packages (from prompt-toolkit<3.1.0,>=3.0.41->ipython>=6.1.0->ipywidgets) (0.2.5)
    Requirement already satisfied: executing in c:\users\sara ishak\anaconda3\lib\site-packages (from stack-data->ipython>=6.1.0->ipywidgets) (0.8.3)
    Requirement already satisfied: asttokens in c:\users\sara ishak\anaconda3\lib\site-packages (from stack-data->ipython>=6.1.0->ipywidgets) (2.0.5)
    Requirement already satisfied: pure-eval in c:\users\sara ishak\anaconda3\lib\site-packages (from stack-data->ipython>=6.1.0->ipywidgets) (0.2.2)
    


.. parsed-literal::

    VBox(children=(IntSlider(value=3, description='Arrival Rate:', max=10, min=1), IntSlider(value=2, description=…



.. parsed-literal::

    Output()


