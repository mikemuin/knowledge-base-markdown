### Ambiguities Requiring Resolution:

1. **User Authentication System:**
   - User recommended for scope of the application.
   - No MFA support or SSO needed
2. **File Attachments:**
   - No file attachments for now
3. **Notifications System:**
   - No email notifications for now
4. **Recurring Meeting Events:**
   - No recurring meeting events for now
5. **Risk Scoring Calculation:**
   - Ignore this feature for now
6. **Project Journal Entity Linking:**
   - Journal entries can link to multiple entities simultaneously
   - Should be queryable from both sides
7. **Soft Delete Implementation:**
   - Use Laravel's default `deleted_at` timestamp approach
8. **Frontend Framework Version:**
   - Livewire 4
9. **Testing Strategy:**
   - Pest
10. **API Versioning:**
    - Versioned from the start. Use `api/v1`
11. **Week Calculation Display Format:**
    - Derive from ISO
12. **Immutability Enforcement:**
    - For immutable entities (Notes, Decisions when locked, published StatusReports), should we:
      - Throw exceptions on update attempts?
      - Silently ignore updates?
      - Return validation errors?
13. **Portfolio Dashboard Data Volume:**
    - Expected number of concurrent projects (10s, 100s, 1000s)?
    - Pagination strategy for large datasets?
    - Caching strategy for expensive aggregations?
14. **Localization/Internationalization:**
    - Is multi-language support required?
    - Date/time format preferences?
    - Currency format for budget tracking (if applicable)?
15. **Backup & Disaster Recovery:**
    - Automated backup strategy required?
    - Point-in-time recovery needed?
    - Multi-region deployment considerations?