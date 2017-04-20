# Glacier

## Overview
- Extermely low-cost storage service that provides durable, secure, and flexible storage for data archiving and online backups
- Designed for rarely accessed data
  - Retrieval time:  3-5 hours

## Common uses
- Long-term storage
  - Tape backups
  - Compliance purposes

## Archives
- Data storage unit in Glacier
  - Can be up to 40TB
  - Each archive has a unique ID that is made at creation time and can not be made "user-friendly"
  - All archives are encrypted and immutable
- Glacier can have an unlimited number of Archives

## Vaults
- Containers for Archives
- Each account can have up to 1000 Vaults
- IAM or vault policies can be used to restrict access

## Vault locks
- Used to deploy and enforce compliance controls
  - Example:  Write Once Read Many (WORM)
- Once locked, the policy can not be modified

## Data Retrieval
- Up to 5% of data can be retrieved every month
