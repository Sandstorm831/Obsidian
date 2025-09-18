- Here are some basics of `streamlit` that will get most work done
```python
# pip install streamlit

import streamlit as st
from time improt sleep
def func():
	st.state_session.is_locked = True

if "is_locked" not in st.session_state:
	st.session_state.is_locked = False # just initialise a new state var

st.text_input("label_name", key="name", value="default_value") # key helps to access this var by session_state variable

st.number_input(
	"label_name", 
	key="longitude", 
	value=0.0, # default value + float
	format="%.6f", # 6 degits after decimal
	step = 0.000001 # step size
)

expander = st.expander("my_expander") # created a expandable section
expander.text("hello, this is text") # text placed inside expandable section

st.button(
	"submit",
	type="secondary", # button style type
	on_click=func,
	disabled=st.state_session.is_locked # checks if disabled
)
```
- To start the server, you use 
```python
python3 -m streamlit run ./path/to/streamlit.py
```

