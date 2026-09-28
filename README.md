website version of vent sim using the original TS code from the vite mobile lite, for drs to mess with, should match the python as well

##Metrics

- VT (mL) = VT/kg × PBW
- Driving pressure = VT / C
- Plateau pressure = PEEP + Driving pressure
- Inspiratory flow = max(0.35, (VT / 1000) / 0.85) (L/s; assumes a 0.85 s inspiration)
- Peak pressure = Plateau + Inspiratory flow × R + |E| × 0.35
- Minute ventilation = VT × RR / 1000 (L/min)
- Oxygenation proxy = clamp(58 + 34·FiO2 + 0.42·PEEP − 0.8·max(0, Plateau − 30), 55, 99)

## Goals

- vt : 4 ≤ VT/kg ≤ 8
- plateau : Plateau < 30
- driving : Driving pressure ≤ 15
- oxygen : FiO2 ≤ 0.65  OR  PEEP ≥ 10

## Status 

- if all 4 goals pass : "Lung Protective"
- else if Plateau ≥ 34 or VT/kg > 10 :"High Risk"
- else : "Needs Review"

## Waveforms

Waveforms display only, don't model physics 

- Effort Dip (when E < 0)
- dip = sin(min(b / 0.12, 1) · π) · E

#### Inspiration (b < 0.34), with t = b / 0.34:
- Pressure = PEEP + (Peak − PEEP) · (1 − e^(−4t)) + dip
- Flow = 1 − 0.25t
- Volume = VT · smoothstep(t)

#### Expiration (b ≥ 0.34), with t = (b − 0.34) / 0.66:
- Pressure = PEEP + (Plateau − PEEP) · e^(−5t)
- Flow = −0.9 · e^(−4.6t)
- Volume = VT · (1 − smoothstep(t))

smoothstep(t) = t²(3 − 2t)

## Ranges 
- Pressure: −10 up to max(50, Peak + 8)
- Flow: −1.2 to 1.2 L/s
- Volume: 0 to 850 mL, so any VT above 850 mL runs off the top of the plot
