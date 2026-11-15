# STORMWATCH: Eastbank Flood Response

**Learn data science by trying to keep a coastal hospital connected during a flood.**

> **[OPEN THE PLAYER GUIDE, LEARNING OUTCOMES & PLAYTEST EVIDENCE](HOW_TO_PLAY.md)**  
> Step-by-step instructions, concepts covered in each task, gameplay screenshots, and peer feedback.

STORMWATCH is a turn-based flood-response learning game. Players investigate noisy rainfall readings, tune a moving average, experiment with a linear-regression model, place a small number of emergency resources on a live city map, then make six rainfall forecasts as the Python flood simulation advances through a 24-hour storm.

## Play loop

1. **Read the rain.** Choose a weather station and adjust the moving-average window. Compare the raw readings with the smoothed signal. Wider windows reduce variation but can hide a sudden burst.
2. **Build a model.** Explore rainfall versus water depth. Training observations and later, unseen test observations have different colors. Move the trial regression line and ask Python to score its average residual against the training data; compare it with Python's least-squares line.
3. **Prepare the city.** Choose pumps, barriers, or a rescue boat from a limited inventory and place them on the map. Their effects are calculated by the flood engine.
4. **Ride the storm.** Make a rainfall estimate, review the model's corresponding water-depth estimate, then lock your call. The simulation advances four hours and reveals the sensor's actual depth. The gap is your prediction error. Repeat for six decisions.
5. **Review the field report.** Compare forecast errors and see whether the hospital road remained open, how much of the road network flooded, and how many residents were evacuated.

Maya Reyes, the field lead, welcomes the player, introduces the mission, and gives step-specific briefings as the game progresses. Players can also open **Ask Maya** at any point for guidance on the current task. At the end, Maya presents a mission score based on forecast accuracy (50%), hospital access (35%), and road safety (15%), along with the individual scores for each category.

The map is interactive: click weather stations to switch the rainfall record, inspect city cells, and place equipment. Drag to pan, use the wheel or map controls to zoom, and turn on **Audio** for optional ambient rain and music.

## Learning goals

- **Moving averages and sampling:** smooth noisy sensor readings; observe how window size changes variance and can hide short-lived peaks.
- **Linear regression and residuals:** connect rainfall input to water-depth output; compare a player-adjusted line with a least-squares fit.
- **Testing predictions and decision-making:** forecast before advancing the flood, measure the miss against a held-out simulated outcome, and use the consequences to judge equipment placement.

All weather is synthetic and reproducible. The city and learning models are educational simulations, not operational flood forecasts.

For the step-by-step player guide, task learning outcomes, gameplay screenshots, and peer feedback, see [How to Play and What You Learn](HOW_TO_PLAY.md).

## Run locally on Windows

Requirements: Windows 10/11, PowerShell, Python 3.11+, and Node.js 20+.

```powershell
Set-ExecutionPolicy -Scope Process Bypass
.\scripts\setup.ps1
.\scripts\run.ps1
```

Open <http://127.0.0.1:5173>. The frontend uses Vite; the Python FastAPI simulation runs at <http://127.0.0.1:8000>. The game needs no API keys or external data service. `Ctrl+C` in the launcher terminal stops services started by it.

The only environment setting is optional. Copy `frontend/.env.example` to `frontend/.env.local` to change the Vite API proxy target; the default is `http://127.0.0.1:8000`. Do not commit `.env.local` or other files containing local secrets. The backend currently needs no environment file.

### Run the services separately

Start the backend from the project root:

```powershell
cd backend
..\.venv\Scripts\python.exe -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

In a second terminal, start the frontend:

```powershell
cd frontend
npm ci
npm run dev
```

For a production container, run `docker build -t stormwatch .` from the project root, then `docker run --rm -p 10000:10000 stormwatch`. The container serves the built frontend and API together. A Render deployment is configured in `render.yaml`.

## Project structure

- `frontend/src/main.ts` — game flow, visual learning challenges, and UI state
- `frontend/src/EastbankScene.ts` — animated Phaser city map, water, and equipment placement
- `frontend/src/style.css` — responsive field-console interface
- `frontend/src/api.ts` — typed client for the Python service
- `frontend/.env.example` — optional Vite API proxy configuration
- `backend/app/analytics/` — synthetic weather, moving averages, regression, and evaluation
- `backend/app/simulation/` — authoritative flood engine and response resources
- `backend/app/main.py` — FastAPI endpoints for game state and analytics
- `shared/eastbank_grid.json` — shared city map and simulation grid

Run `npm run build` from `frontend/` to build the frontend. Run backend checks from `backend/` with `..\.venv\Scripts\python.exe -m pytest -q`.
