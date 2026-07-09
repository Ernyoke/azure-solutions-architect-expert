# Disaster Recovery Concepts

## Hot/Cold

- Hot:
    - Failover to secondary site happens automatically with no downtime
    - No data loss
    - Requires duplicate infrastructure
    - The most expensive DR method
- Cold:
    - Failover to secondary site takes some time and might be manual
    - Some data might be lost
    - Less expensive

## RPO/RTO/RLO

- RPO - Recovery Point Objective:
    - How much data we allow ourselves to lose in case of a disaster => what is the frequency of data sync to the secondary region
    - Usually measured in minutes
- RTO - Recovery Time Objective:
    - How much downtime we can tolerate in case of a disaster => how long should it take before a system is up again
    - Usually measured in minutes
- RLO - Recovery Level Objective
    - Level of recovery required after an outage
    - Varies depending on the type of data or application being recovered
    - A recovery solution should be able to restore the data/application to the required RLO level, e.g. if RLO requires point-in-time recovery restor, the recovery solution should be able to restore data to the specific point in time
