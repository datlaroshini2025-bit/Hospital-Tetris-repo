🏥 Hospital Tetris
"Break the hospital. Watch it recover."

Hospital Tetris is an operational digital twin prototype that models a dynamic hospital system using public healthcare datasets from Kaggle to calibrate a real-time synthetic discrete-event simulation.

Built for high-stakes healthcare operations, Hospital Tetris allows clinical command teams and hospital administrators to watch operations in real time, intentionally trigger stress disruptions (e.g., a 15-casualty multi-trauma surge or diagnostic analyzer failure), watch operational bottlenecks propagate across beds and queues, and test deterministic optimization interventions—with explainable AI reasoning—before applying them in the real world.

⚡ The Core Demo Experience
Nominal State: Watch the 2D topological hospital digital twin running smoothly. Inpatients occupy beds, walk-ins and ambulances arrive, triage categorizes acuity (ESI Levels 1–5), diagnostic specimens flow to the laboratory, and convalescing patients discharge.
Break the Hospital: Trigger 🚨 MASS CASUALTY. A burst of 15 acute trauma casualties arrives via EMS.
Bottleneck Propagation:
Emergency trauma bays hit 100% capacity.
ICU beds fill completely; acute patients begin boarding in hallways.
Laboratory queues expand with STAT bloodwork orders.
Average waiting times spike.
Deterministic Optimization: Click ⚡ OPTIMIZE. The solver analyzes constraints, calculates surge bed conversions and staff reallocations, and presents a Before vs Proposed After preview modal with zero live mutation.
Explainable AI (Why?): Inspect audited reasoning answering: What happened? Why was this intervention selected? What operational constraints bound it? What simulated effect is expected?
Recovery: Click [Apply Optimization]. Surge beds open, cross-trained staff reallocate, queue backlogs clear, and the hospital stabilizes.
What-If Sandbox: Fork an isolated clone of the hospital to ask counterfactual questions ("What if laboratory capacity drops by 50%?" or "What if we commission 4 extra ICU beds?") and compare 30-minute trajectories side-by-side on interactive Recharts curves.
🏛️ Source-of-Truth Architecture
Public Healthcare Data (Kaggle)
            │
            ▼
 [Data Ingestion & Cleaning]
            │
            ▼
[Feature Extraction & Statistical Calibration]
            │
            ▼
[Dataset-Derived Parameters] (Immutable baselines)
            │
            ▼
[Seeded Deterministic Simulation Engine] ◄─── [Scenario Triggers: Mass Casualty, Lab Fault]
(Ticks, Beds, Staff, Patient Pathways)
            │
            ▼
[Deterministic Bottleneck Detector]
(Utilization > 85%, Queue Spikes, Downstream Bed Lock)
            │
            ▼
[Deterministic Optimization Engine]
(Staff Rebalancing, Surge Beds, Priority Queuing)
            │
            ├───────────────────────────────┬──────────────────────────────┐
            ▼                               ▼                              ▼
[Before vs After State Projection]   [Structured Decisions]    [What-If Counterfactual Sandbox]
            │                               │                              │
            ▼                               ▼                              ▼
[Optimization Preview Modal]       [Explainable AI Auditor]        [Forked Simulation Engine]
[Apply / Discard Actions]          (What, Why, Constraints)        (Side-by-Side Trajectories)
Important

Strict Separation of Concerns:

RAW DATA: Public de-identified Kaggle healthcare datasets (Emergency wait times, Inpatient Length of Stay, Diagnostic turnaround times).
DATASET-DERIVED PARAMETERS: Frozen mathematical distributions (hourly arrival curves, ESI triage acuity distributions, department cycle durations).
SIMULATION STATE: Real-time discrete-event twin state (active patients, bed allocations, on-duty staff, queue tokens).
AI LAYER: Explains already-computed deterministic solver decisions. The AI layer never hallucinates metrics or directly mutates patient routing.
📊 Kaggle Datasets & Calibration Pipeline
Hospital Tetris uses public Kaggle healthcare datasets to anchor simulation mechanics in real-world clinical baselines:

Kaggle Reference Benchmark	Records	Features Extracted	Simulation Parameter Calibrated
Hospital Emergency Department Inflow & Triage Wait Times	14,250	arrival_hour, esi_score, door_to_triage_wait, lab_ordered	Hourly arrival curve multipliers (10 AM peak = 1.48x), ESI Triage distribution (Level 1: 8%, Level 2: 22%, Level 3: 42%, Level 4: 20%, Level 5: 8%), STAT lab probability (96% for Level 1).
Inpatient Hospital Stays & ICU Bed Utilization (MIMIC-derived)	8,900	department_type, los_hours, bed_occupancy_ratio	Baseline bed utilization warning threshold (85%), ICU length-of-stay distribution, General Ward length-of-stay distribution.
Clinical Diagnostic Laboratory & Radiology Turnaround Analysis	5,640	test_modality, turnaround_minutes, queue_depth	Calibrated laboratory cycle time (5 sim min) and CT/X-Ray imaging turnaround time (7 sim min).
Inspect the live calibration pipeline inside the app by clicking the "Kaggle Calibrated" badge in the top navigation bar.

🏥 Clinical Topology & Patient Flow
Synthetic patients navigate a continuous operational graph across 10 specialized departments:

                  [ AMBULANCE BAY ] (EMS intake dock)
                          │
                          ▼
                 [ EMERGENCY DEPT ] (Trauma bays, 12 capacity)
                          │
                          ▼
                 [ TRIAGE CENTER ] (ESI scoring, 8 capacity)
                          │
         ┌────────────────┼────────────────┐
         ▼                ▼                ▼
     [  ICU  ]     [ GENERAL WARD ]  [ PEDIATRIC WARD ]
   (6 ICU Beds)     (16 Ward Beds)     (8 Peds Beds)
         │                │                │
         └────────────────┼────────────────┘
                          ▼
            [ LABORATORY ] & [ IMAGING ] (Diagnostics)
                          │
                          ▼
                    [ PHARMACY ] (Medication dispensing)
                          │
                          ▼
                   [ DISCHARGE ] (Convalescence & exit)
🛠️ Technology Stack
Frontend Core: React 19, TypeScript, Vite 8
Styling & UI: Tailwind CSS v4 (@tailwindcss/postcss), Lucide React
Motion & Micro-interactions: Framer Motion
Data Visualizations: Recharts (Responsive Before vs After comparison trajectories)
Mathematical Simulation: Mulberry32 Seeded Pseudo-Random Number Generator (PRNG) for 100% deterministic runs and What-If state cloning
