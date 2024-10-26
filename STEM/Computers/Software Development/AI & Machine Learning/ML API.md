- 
- .
    ```
    pip install uvicorn gunicorn fastapi pydantic sklearn pandas pickle
    ```
- gunicorn ⇒ heroku
- pydantic ⇒ structure request, defining request models
- .
    ```
    uvicorn mlapi:app --reload
    ```
- store ML model in pkl file
- .
    ```
    with open('rfmodel.pkl', 'rb') as f:
    model = pickle.load(f)
    ```
- 
