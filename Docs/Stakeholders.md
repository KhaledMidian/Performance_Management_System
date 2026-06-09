# Roles and Business Purpose

| Role | Business Purpose |
|------|------------------|
| System Administrator | Owns the overall KPI Management System, ensuring organizational structure, users, roles, and system settings are correctly maintained and governed. |
| Department Manager | A department lead responsible for defining performance objectives, creating KPIs, assigning them to employees, and reviewing submitted measurements. |
| Employee | An individual contributor responsible for executing assigned objectives, submitting KPI measurements, and monitoring personal performance progress. |
| Executive | A senior decision-maker responsible for monitoring organizational performance through dashboards, reports, and strategic analytics without participating in operational data entry. |

---

# Permission Legend

| Label | Meaning |
|---------|---------|
| Own Only (Created) | User can only access records they personally created |
| Own Only (Dept) | User can only access records within their department |
| Own Only (Self) | User can only access their own profile or personal records |
| Own Only (Assigned) | User can only access records explicitly assigned to them |
| ✅ | Permission granted |
| ❌ | Permission not granted |

---

# Capability Matrix

| Entity | Operation | System Administrator | Department Manager | Employee | Executive |
|----------|----------|----------|----------|----------|----------|
| **Department** | Create | ✅ | ❌ | ❌ | ❌ |
| | Read (Own) | ✅ | Own Only (Dept) | ❌ | ❌ |
| | Read (All) | ✅ | ❌ | ❌ | ✅ |
| | Update | ✅ | ❌ | ❌ | ❌ |
| | Delete | ✅ | ❌ | ❌ | ❌ |
| | Approve/Reject | ❌ | ❌ | ❌ | ❌ |
| **User** | Create | ✅ | ❌ | ❌ | ❌ |
| | Read (Own) | ✅ | Own Only (Dept) | Own Only (Self) | Own Only (Self) |
| | Read (All) | ✅ | Own Only (Dept) | ❌ | ✅ |
| | Update | ✅ | ❌ | Own Only (Self) | ❌ |
| | Delete | ✅ | ❌ | ❌ | ❌ |
| | Approve/Reject | ❌ | ❌ | ❌ | ❌ |
| **KPI** | Create | ✅ | ✅ | ❌ | ❌ |
| | Read (Own) | ✅ | Own Only (Created) | Own Only (Assigned) | ❌ |
| | Read (All) | ✅ | Own Only (Dept) | ❌ | ✅ |
| | Update | ✅ | Own Only (Created) | ❌ | ❌ |
| | Delete | ✅ | Own Only (Created) | ❌ | ❌ |
| | Approve/Reject | ❌ | ❌ | ❌ | ❌ |
| **Measurement** | Create | ✅ | ❌ | Own Only (Assigned) | ❌ |
| | Read (Own) | ✅ | Own Only (Dept) | Own Only (Assigned) | ❌ |
| | Read (All) | ✅ | Own Only (Dept) | ❌ | ✅ |
| | Update | ✅ | ❌ | Own Only (Assigned) | ❌ |
| | Delete | ✅ | ❌ | Own Only (Assigned) | ❌ |
| | Approve/Reject | ❌ | ✅ | ❌ | ❌ |
| **Notification** | Create | ❌ | ❌ | ❌ | ❌ |
| | Read (Own) | Own Only (Self) | Own Only (Self) | Own Only (Self) | Own Only (Self) |
| | Read (All) | ✅ | ❌ | ❌ | ❌ |
| | Update | ❌ | ❌ | ❌ | ❌ |
| | Delete | ❌ | ❌ | ❌ | ❌ |
| | Approve/Reject | ❌ | ❌ | ❌ | ❌ |

---

# Notes

1. **Department Managers are restricted to their own department.**
   They may view employees, KPIs, and measurements that belong to their department but cannot access data from other departments.

2. **Managers can only modify KPIs they personally created.**
   This prevents one department manager from altering another manager's strategic objectives.

3. **Employees can only access KPIs explicitly assigned to them and measurements submitted against those assignments.**
   They cannot modify KPI definitions or access performance data belonging to other employees.

4. **Measurement approval is performed exclusively by Department Managers.**
   Submitted measurements become official only after managerial review.

5. **Executives have organization-wide read access to strategic and performance data.**
   They can view departments, users, KPIs, and measurements but cannot perform operational actions.

6. **System Administrators have unrestricted administrative access.**
   Their responsibilities include user management, role management, organizational setup, and system governance.

7. **Notifications are system-generated artifacts.**
   No user role can directly create notifications. Notifications are automatically produced in response to business events such as KPI assignment, measurement approval, measurement rejection, or scheduled reminders.

8. **Users can only view notifications addressed to themselves.**
   This protects privacy and prevents exposure of personal approval/rejection messages.

9. **Executives receive their own notifications only.**
   They cannot access notifications sent to managers or employees.

10. **Approval/Reject permissions exist only for Measurements.**
    Departments, Users, KPIs, and Notifications do not participate in an approval workflow within the current business model.

11. **Assignment-based permissions are distinct from ownership-based permissions.**
    Employees do not own KPIs; they are granted access only to KPIs assigned to them and the measurements associated with those assignments.
