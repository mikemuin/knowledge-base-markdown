# Architecture Idea

## Forms + Data + Analytics

To build a software with Forms Engine + Hybrid EAV (with JSON) data models, the following components are needed:

1. Presentation Layer
   1. Forms Rendering
   2. Forms JSON Schema
   3. Forms Data Model
      1. Nesting
      2. Permissions
      3. Life Cycle
2. Data Storage Layer
   1. Terminology Services
   2. Clinical Concepts (per Field or Variable)
   3. Hybrid and Typed EAV Tables
   4. Metadata Tables
3. Data Analytics Layer
   1. Indexed Data Marts
   2. Generated Columns (Virtual and Stored)
   3. Materialized Views
   4. Triggers and Procedures
   5. Data Warehouse

## Architectural Hierarchy

Within the context of clinical workflows and clinical registries, forms and data do not exist in isolation. They form part of an interdependent whole. We call this top unit a **module**.

Here's the hierarchy of units:

1. Modules
2. Forms
3. Pages
4. Sections
5. Form Components / Form Widget
   - This is the atomic unit of form functionality and the Lego block for building modules
   - Customizations can be done at this level:
     - Permissions
     - Life Cycle (including Draft Status)
     - Anchor Point (Patient, Encounter, Module) Attachments
6. Questions
   1. Linked to Concepts and Terminologies
7. Answers
   1. Linked to Concepts and Terminologies

External to the modular hierarchy in each module are the following:

- Reports
- Dashboards