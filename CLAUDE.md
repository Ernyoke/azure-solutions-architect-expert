# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a documentation repository containing study notes for the **Azure Solutions Architect Expert (AZ-305)** certification exam. The content is organized as a structured learning resource covering Azure services, concepts, and best practices.

## Repository Structure

The repository follows a numbered directory structure representing different topics:

- `00-basic-concepts/` - Foundational Azure concepts (regions, resource groups, cost, SLA, storage)
- `01-azure-compute/` - Azure compute services (VMs, VM Scale Sets, ARM templates)
- Additional numbered directories will follow the same pattern as the study progresses

Each directory contains markdown files covering specific topics within that category.

## Content Organization Principles

1. **Hierarchical Learning Path**: Topics are numbered to indicate learning sequence (00, 01, 02, etc.)
2. **One Topic Per File**: Each markdown file focuses on a single concept or service
3. **Structured Format**: Notes follow a consistent structure:
   - Main concept definition
   - Key characteristics and use cases
   - Specific details (pricing, availability, configuration options)
   - Best practices and naming conventions where applicable

## Naming Conventions

- **Resource Groups**: Should include `rg` or `RG` as prefix or suffix
- **Files**: Use lowercase with hyphens (e.g., `resource-group.md`, `vm.md`)
- **Directories**: Use numbered prefixes with descriptive names (e.g., `00-basic-concepts`)

## Key Azure Concepts Covered

### Cost Management
- Always verify costs before provisioning resources
- Consider cost-effective alternatives (e.g., Standard SSD vs Premium SSD)
- Use Azure Pricing Calculator: https://azure.microsoft.com/en-us/pricing/calculator/
- Leverage cost-saving strategies: Reserved Instances, Spot Instances, Auto-shutdown

### SLA Calculations
- Combined SLA = Product of individual service SLAs
- Example: 99.95% × 99.99% = 99.94% actual SLA

### Availability Architecture
- **Fault Domain**: Physical hardware sharing common power (rack level)
- **Update Domain**: Hardware that can be rebooted simultaneously for maintenance
- **Availability Set**: Spread VMs across up to 3 Fault Domains and 20 Update Domains (single AZ)
- **Availability Zone**: Physically separate data centers within a region (best SLA)

## Content Guidelines When Adding/Editing

1. **Be Concise**: Keep notes focused and exam-relevant
2. **Include Specifics**: Add concrete numbers (SLA percentages, cost savings, time ranges)
3. **Use Bullet Points**: Notes are structured for quick reference
4. **Highlight Key Decisions**: Document factors that influence architecture choices (cost, availability, region selection)
5. **Link Related Concepts**: Cross-reference when topics connect (e.g., VM cost relates to disk types)

## Git Workflow

This is a personal study repository tracked in Git. Standard Git workflow applies:
- Commit logical changes with descriptive messages
- Track progress through commits as study material evolves
