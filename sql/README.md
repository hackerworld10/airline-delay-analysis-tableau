These 3 SQL statements are in data_cleaning.sql.

1️⃣ Clean base view (MOST IMPORTANT)

This removes cancelled & diverted flights and keeps only what matters.

CREATE VIEW clean_airline_data AS
SELECT
    year,
    month,
    carrier,
    carrier_name,
    airport,
    airport_name,
    arr_flights,
    arr_del15,
    arr_delay,
    carrier_delay,
    weather_delay,
    nas_delay,
    security_delay,
    late_aircraft_delay
FROM airline_delay_cause
WHERE arr_cancelled = 0
  AND arr_diverted = 0;

2️⃣ Airport Reliability (CORE DASHBOARD METRIC)

This directly supports “Ranking airports by reliability.

CREATE VIEW airport_reliability AS
SELECT
    airport,
    airport_name,
    SUM(arr_flights) AS total_flights,
    ROUND(
        (SUM(arr_flights) - SUM(arr_del15)) * 100.0
        / SUM(arr_flights),
        2
    ) AS on_time_percentage
FROM clean_airline_data
GROUP BY airport, airport_name;

3️⃣ Delay Cause Contribution (CAUSE ANALYSIS)

Used for delay cause charts.

CREATE VIEW delay_cause_summary AS
SELECT
    airport,
    airport_name,
    SUM(carrier_delay) AS carrier_delay_minutes,
    SUM(weather_delay) AS weather_delay_minutes,
    SUM(nas_delay) AS nas_delay_minutes,
    SUM(security_delay) AS security_delay_minutes,
    SUM(late_aircraft_delay) AS late_aircraft_delay_minutes
FROM clean_airline_data
GROUP BY airport, airport_name;
