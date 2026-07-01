# ChildCare Schedules — Technical Integration Brief

ChildCare Schedules is an offline staff scheduling app backed by a formally verified exact-cover scheduling core.

## Exact-cover formulation

The scheduler converts childcare staffing rules into an exact-cover problem:

- Required classroom/time-period coverage cells are represented as primary constraints.
- Candidate assignments, such as assigning a staff member to a classroom, floater duty, break cover, lunch cover, or prep cover, are represented as rows.
- Availability, home-room preferences, age-group eligibility, floater rules, and off-floor obligations are represented as compatibility constraints and row metadata.
- A valid schedule is a set of selected rows that covers the required constraints exactly while avoiding conflicts such as placing the same staff member in two places at the same time.

## Formal verification claim

The verified core provides a soundness guarantee for returned schedules: if the engine returns a schedule, the selected assignments satisfy the encoded rules for that bounded run.

The app does not claim completeness for every bounded run. Because practical search fuel and bounds are used, “no schedule found” means no schedule was found within the configured bounds, not necessarily that no schedule exists mathematically.

## Device-scale behavior

Recent phone tests show that schedules around 70 teachers across 20 classrooms can finish in about 7 minutes. Close-to-100 staff schedules may take significantly longer, especially with covered breaks, lunch, prep, or other off-floor obligations.

The reason is combinatorial: the exact-cover search space grows exponentially as more staff, rooms, time slots, and cover obligations are combined. Very large covered-duty runs can consume too much memory. On iPhone, iOS may terminate a heavy local run under memory pressure or thermal protection. This is a device resource limit, not a soundness failure.


## Mapping templates and user responsibility

The UI may generate starter maps such as room-to-group, staff-to-home-room, and staff-to-allowed-group maps. These are not inferred facts about the center. They are typing templates derived from the current roster, room list, group names, and simple default assumptions.

For production integrations, the host product should treat these generated maps as draft data. The operator or integrating system must supply or confirm the real mappings: classroom age/eligibility group, staff home-room assignment if used, and staff certifications or allowed age groups. The solver then treats the confirmed maps as constraints.

## Integration model

A childcare platform can integrate the scheduling core by mapping existing product data into a normalized scheduling problem:

- Staff roster
- Classroom roster
- Staff availability
- Classroom demand by time period
- Staff eligibility and home-room preferences
- Floater/relief staffing rules
- Break, lunch, prep, and off-floor duty policies

The engine then returns a schedule that the host platform can render, export, audit, or adjust in its own UI.
