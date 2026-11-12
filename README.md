# Azure Portfolio: Hands-On Labs for IT Support

Four beginner-friendly Azure labs I built and documented myself on a free trial subscription, covering identity, virtual machines, storage and cost control. Each lab was built step by step, tested, and cleaned up afterwards.

**Author:** Wasiu Shittu | [LinkedIn](https://linkedin.com/in/wasiu-shittu) | [GitHub](https://github.com/Lordwaz)

## Labs at a glance

| Lab | Topic | Key skills | Date |
|---|---|---|---|
| 1 | Entra ID identity | Users, groups, role assignment, least privilege | 11 Nov 2026 |
| 2 | Ubuntu VM | Virtual machines, SSH, network security groups, break/fix | 12 Nov 2026 |
| 3 | Storage account | Blob storage, redundancy, private containers | 12 Nov 2026 |
| 4 | Budget alert and cleanup | Cost Management, alerts, resource group deletion | 12 Nov 2026 |

A day-by-day log, including mistakes and fixes, is in [JOURNEY.md](JOURNEY.md). Screenshots are in the `screenshots/` folder, one subfolder per lab.

## Repository structure

```
azure-portfolio/
  README.md          (this file: all labs, steps and results)
  JOURNEY.md         (dated log of what I did and what went wrong)
  screenshots/
    lab1/
    lab2/
    lab3/
    lab4/
```

---

## Lab 1: Entra ID Identity and Least Privilege

**Goal:** Create test users and a group in Microsoft Entra ID, give the group a limited admin role, and confirm what that role can and cannot do.

**Skills shown:** Identity management, groups, role-based access, least-privilege principle, the kind of tasks a help desk handles every day.

### Environment
| Item | Value |
|---|---|
| Test users | `alice-test`, `bob-test` |
| Group | `Lab-Helpdesk` |
| Role tested | Helpdesk Administrator |

### Steps
1. Opened the Azure portal and Microsoft Entra ID for the tenant.
2. Created two test users, `alice-test` and `bob-test`.
3. Created the group `Lab-Helpdesk` and added the users to it.
4. Assigned the **Helpdesk Administrator** role and tested what a user with that role can do compared with a full administrator.

### Results
- Users and group created and working.
- Confirmed that a Helpdesk Administrator can do help desk tasks (such as password resets for non-admin users) but not full tenant administration. This shows why giving people only the access they need matters.
- The users and group were kept for later labs.

**Screenshots:** `screenshots/lab1/`

---

## Lab 2: Ubuntu Virtual Machine and Network Security Group

**Goal:** Deploy a small Linux VM, restrict access to it, and practise a break/fix on the network rules.

**Skills shown:** Virtual machines, SSH access, network security groups (NSG), troubleshooting a connectivity problem.

### Environment
| Item | Value |
|---|---|
| VM name | `vm-lab2` |
| OS | Ubuntu |
| Size | Standard_B2ts_v2 |
| Region | South Africa North |
| Resource groups | `vm-lab2_group` (VM and network resources), `rg-lab2` |
| SSH access | Locked to my own IP address only |

### Steps
1. Created the Ubuntu VM with a small, low-cost size in South Africa North.
2. Set up SSH access and restricted the NSG rule to my own public IP rather than the whole internet.
3. Connected to the VM over SSH to confirm access.
4. Ran a **break/fix** exercise: changed the NSG rule so SSH stopped working, confirmed the failure, then corrected the rule and restored the connection.
5. Stopped (deallocated) the VM when finished so it would not keep charging for compute.

### Results
- VM deployed and reachable only from my IP.
- Break/fix completed: I could diagnose a blocked connection by looking at the NSG rules and fix it.
- VM stopped after the lab to avoid cost.

**Screenshots:** `screenshots/lab2/`

---

## Lab 3: Storage Account (Blob Storage)

**Goal:** Create an Azure storage account, store a file in a private container, and confirm the data is not publicly reachable.

**Skills shown:** Azure Storage, blob containers, redundancy options, access control, cost awareness.

### Environment
| Item | Value |
|---|---|
| Resource group | `rg-lab3` |
| Storage account | `stlab3wasiu` |
| Region | South Africa North |
| Performance | Standard |
| Redundancy | LRS (changed from GRS, see below) |
| Container | `lab3-files` (Private) |

### Steps
1. **Create the storage account.** Storage accounts > Create. Set the subscription, create the new resource group `rg-lab3`, name the account, choose the region, set **Primary service** to Azure Blob Storage or Azure Data Lake Storage Gen 2, performance to Standard, then Review + create.
2. **Fix the redundancy.** The account was created with **Geo-redundant storage (GRS)** instead of the intended LRS, because the form reset before I clicked Create. I corrected it after deployment: storage account > **Data management > Redundancy** > Locally-redundant storage (LRS) > Save. The secondary region (South Africa West) disappeared from the replication table, which confirmed the change.
3. **Create a container.** Data storage > Containers > + Add container. Name: `lab3-files`, anonymous access level: **Private**.
4. **Upload a test file.** Opened `lab3-files` > Upload > selected a small text file. It appeared as a Block blob in the Hot access tier.
5. **Verify privacy.** The container list shows `lab3-files` with **Anonymous access level: Private**. The account overview shows **Blob anonymous access: Disabled**, secure transfer required, and minimum TLS 1.2.

### Results
- Storage account deployed successfully (provisioning state: Succeeded).
- Redundancy corrected from GRS to LRS, which is cheaper and sufficient for a lab.
- Test blob uploaded and stored encrypted at rest (server encrypted: true).
- No anonymous access is possible: the container is Private and account-level blob public access is disabled.

### Troubleshooting notes
- **Validation error on Primary service.** The Review + create page failed because Primary service was blank. Selecting a value on the Basics tab fixed it.
- **Wrong resource group.** The form defaulted to the Lab 2 resource group. I created `rg-lab3` so each lab can be cleaned up independently.
- **Chrome "Dangerous site" warning.** Opening the blob URL directly was blocked by Chrome Safe Browsing, which flags the shared `blob.core.windows.net` domain generally. It is not a problem with my account or file. Because the browser blocked the request before Azure could answer, the URL test could not prove privacy, so I verified it through the container and account settings instead.

**Screenshots:** `screenshots/lab3/` (review page, deployment complete, redundancy showing LRS, blob properties, containers list showing Private)

---

## Lab 4: Budget Alert and Cost Cleanup

**Goal:** Set a spending alert so the free trial credit is protected, then delete all lab resources.

**Skills shown:** Azure Cost Management, budgets and alerts, resource group lifecycle, cost hygiene.

### Part 1: Budget alert
| Setting | Value |
|---|---|
| Budget name | `budget-lab4` |
| Scope | Azure subscription 1 |
| Amount | £5.00 |
| Reset period | Monthly |
| Alert 1 | Actual cost at 80% of budget (£4) |
| Alert 2 | Forecasted cost at 100% of budget |
| Notification | Email to my address |

**Steps**
1. Opened Cost Management and set the scope to the subscription. Budgets are not available at the billing-account scope, so I opened the budget page from **Subscriptions > Azure subscription 1 > Cost Management > Budgets**.
2. Budgets > + Add. Entered the name, monthly reset, and a £5 amount.
3. On Set alerts, added one **Actual cost** condition at 80% and one **Forecasted cost** condition at 100%, plus an alert email address.
4. Created the budget. It now appears in the Budgets list (£5.00, Monthly).

**Troubleshooting notes**
- The Budgets menu was missing at first because the scope was the billing account, not the subscription.
- Create failed with "Action group or at least one email address is required" until a recipient was added.
- A second error, "Unique email address is required", was caused by entering the same address twice. Removing the duplicate fixed it.

### Part 2: Cleanup
Deleted every resource group created in Labs 2 and 3:
- `vm-lab2_group` (VM, disk, public IP, NSG, network interface, virtual network)
- `rg-lab2` (empty)
- `rg-lab3` (storage account)

For each: open the resource group > **Delete resource group** > type the group name to confirm > Delete. The final Resource groups list shows only `NetworkWatcherRG`, which Azure creates automatically and which costs nothing.

**Troubleshooting notes**
- The Delete button is not available from the Resource groups list or its row menu. It only appears in the toolbar inside each resource group, and zooming the browser out (Ctrl and minus) revealed it on a narrow window.
- The list does not refresh by itself after a delete, so deleted groups kept appearing until I clicked Refresh. Opening a deleted group showed "Resource not found", which confirmed the deletion.
- I did not click **Upgrade** in the portal header, so the subscription stays on the free trial and cannot start charging.

### Results
- Budget alert active on the subscription.
- All lab resources deleted, so no compute, storage or IP charges continue.
- Current cost at the time of cleanup was about £0.10 against the free trial credit.

**Screenshots:** `screenshots/lab4/` (budget alert settings, Budgets list, Resource groups list after cleanup)

---

## What I learned
- **Least privilege:** give people only the role they need, and test what that role can and cannot do.
- **Network rules:** a locked-down NSG protects a VM, and most connection problems can be diagnosed by reading the rules.
- **Redundancy:** check the setting before creating a storage account. GRS costs more and can be changed to LRS afterwards, but it is better to get it right first.
- **Privacy checks:** browser warnings can block testing of public blob URLs, so verify privacy through the account and container settings.
- **Cost control:** budgets live at the subscription scope, and deleting a resource group removes everything inside it, which makes cleanup quick and complete.
- **Organisation:** put each lab in its own resource group so cleanup is a single delete.
