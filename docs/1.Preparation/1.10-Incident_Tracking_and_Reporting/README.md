# Incident Tracking Task

## Conditions

- IR team activated with requirement to track all cyber incidents.
- Database or centralized tracking system is available.

## Standards

- Create a database schema that supports the tracking of cyber incidents.
- Record all confirmed true-positive incidents.
- Report confirmed incidents to higher (ACOIC / INTEL).

## Procedural Steps (Checklist)

### Create Incident Tracking Database

- [ ] Determine location for central database (local server, SQL database, Google Sheets, Confluence table).
- [ ] Define schema including:
  - Incident ID (auto-generated preferred)
  - Source ID or handler ID
  - Confirmation Status
  - Incident Summary
  - Related Incidents
  - Confidence Rating
  - Incident Notes

#### Tools
- SQL database (MySQL, PostgreSQL) for structured use
- Google Sheets or Airtable for small teams
- Confluence or Jira for integrated workflow

#### Example SQL Table Creation Script:

```sql
CREATE TABLE incident_tracking (
    incident_id SERIAL PRIMARY KEY,
    source_id VARCHAR(50),
    confirmed BOOLEAN,
    summary TEXT,
    related_incidents TEXT,
    confidence ENUM('Low','Medium','High'),
    notes TEXT
);
```

### Record Confirmed True Positive Incidents

- [ ] For each verified incident:
  - Assign Incident ID
  - Populate all required fields
  - Update incident status
- [ ] Maintain real-time data entry discipline.

### Report to ACOIC (or higher)

- [ ] Create incident summary based on database entry.
- [ ] Follow reporting format (email, daily SITREP).
- [ ] Send via secure means (encrypted email or Teams).

## End State

- All incidents are tracked in standardized database.
- True positives are reported to higher HQ or ACOIC.

## References

- [VERIS: The Vocabulary for Event Recording and Incident Sharing](http://veriscommunity.net/index.html)

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Expanded procedural checklist and SQL example | Leo |
