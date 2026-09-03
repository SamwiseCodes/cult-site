# Workout Data Store

This folder is the durable home for workout records.

## Files

- `workouts.sqlite3` — primary database. It contains:
  - `workout_sessions`: one row per workout session
  - `workout_sets`: one row per exercise set/cardio segment
  - `workout_log`: a convenient combined view
- `workout_log.csv` — portable spreadsheet-friendly export/template.

## Design

A workout session can contain any number of exercises and sets. Strength entries use `reps` and `weight_kg`; cardio entries can use `distance_km` and/or `duration_seconds`. Unused fields may be left blank.

Dates use `YYYY-MM-DD`. Times may be stored as `HH:MM` or full ISO 8601 timestamps. `rpe` and `overall_rating` use a 1–10 scale.

## Example queries

```bash
sqlite3 data/workouts/workouts.sqlite3
```

```sql
-- Recent sessions
SELECT * FROM workout_sessions ORDER BY workout_date DESC LIMIT 10;

-- Complete log, including individual sets
SELECT * FROM workout_log ORDER BY workout_date DESC, set_id;

-- Best recorded weight by exercise
SELECT exercise_name, MAX(weight_kg) AS max_weight_kg
FROM workout_sets
GROUP BY exercise_name
ORDER BY max_weight_kg DESC;
```

Keep `workouts.sqlite3` as the source of truth and refresh `workout_log.csv` when a spreadsheet export is needed.

## Personal records

Personal records are stored separately in the `personal_records` table and exported to `personal_records.csv`. The August 20, 2026 entry is recorded as `30` `reps` of `Push-ups` with record type `consecutive_reps`, linked to workout session 1.

```sql
-- Personal records
SELECT * FROM personal_records ORDER BY record_date DESC;
```
