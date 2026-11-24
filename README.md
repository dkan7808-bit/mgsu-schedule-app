from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
import datetime

app = FastAPI()

# Разрешаем сайту с Vercel обращаться к серверу
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

START_DATE = datetime.date(2026, 9, 7)
DAYS_RU = ["Понедельник", "Вторник", "Среда", "Четверг", "Пятница", "Суббота", "Воскресенье"]

# Твое расписание (я перевел его в формат JSON)
SCHEDULE = {
    1: [
        {"time": "08:30 - 09:50", "name": "Металлические конструкции", "type": "Лекция", "teacher": "Бунов А.А.", "room": "283 Мытищи", "week_type": "odd"},
        {"time": "08:30 - 09:50", "name": "Металлические конструкции", "type": "Лекция", "teacher": "Бунов А.А.", "room": "283 Мытищи", "week_type": "even"},
        {"time": "10:00 - 11:20", "name": "Металлические конструкции", "type": "Практика", "teacher": "Кайдас П.А.", "room": "333 Мытищи", "week_type": "odd"},
        {"time": "10:00 - 11:20", "name": "Методы проектирования...", "type": "КРПТ", "teacher": "Шунько Н.В.", "room": "338 Мытищи", "week_type": "even"},
        # ... сюда нужно добавить все остальные пары ...
    ],
    # ... здесь будут дни 2, 3, 4 ...
}

@app.get("/api/schedule")
def get_schedule(day: int, week_type: str):
    day_schedule = SCHEDULE.get(day, [])
    filtered = [item for item in day_schedule if item["week_type"] == week_type or item["week_type"] == "all"]
    return {"schedule": filtered, "day_name": DAYS_RU[day - 1]}

# Необходимо для Render
if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=10000)
