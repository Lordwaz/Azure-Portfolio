# Azure Portfolio Journey

A dated log of what I built, what went wrong, and how I fixed it. The lab write-ups with full steps are in [README.md](README.md).

---

## 11 November 2026: Getting started and Lab 1

**Setup**
- Opened the Azure portal for the first time. My tenant had no subscription yet, so I started the free trial. At first the credit information failed to load, but this cleared up and the trial became active (about £150 of credit, valid for 30 days).
- Planned a basic set of labs for IT support roles: identity, a virtual machine, storage, and cost control.

**Plan changes**
- The first plan included a Microsoft Sentinel SOC lab and a Conditional Access and MFA lab. I decided this portfolio should stay on basic Azure skills, so I dropped Sentinel and made Conditional Access optional.
- Revised plan: Lab 2 basic VM, Lab 3 storage account, Lab 4 budget alert and cleanup.

**Lab 1: Entra ID identity**
- Created two test users (`alice-test`, `bob-test`) and a group (`Lab-Helpdesk`).
- Assigned the Helpdesk Administrator role and tested the limits of what that role can do, to show the least-privilege principle.
- Kept the users and group for later labs.

---

## 12 November 2026: Labs 2, 3 and 4

**Lab 2: Ubuntu VM and NSG**
- Deployed an Ubuntu VM (`vm-lab2`, Standard_B2ts_v2) in South Africa North.
- Locked SSH access to my own IP address using the network security group.
- Completed a break/fix exercise on the NSG rules.
- Stopped the VM when finished so it would not keep using credit.

**Lab 3: Storage account**
- Resumed from the Storage accounts page and created `stlab3wasiu` in a new resource group, `rg-lab3`.

Problems and fixes:
| Problem | What happened | Fix |
|---|---|---|
| Validation failed | Review + create said "Primary service" was blank | Selected Azure Blob Storage or Data Lake Storage Gen 2 on the Basics tab |
| Wrong resource group | The form showed `rg-lab2` from the previous lab | Created `rg-lab3` so each lab can be deleted separately |
| Wrong redundancy | The account was created as GRS instead of LRS because the form reset | Changed it afterwards under Data management > Redundancy. The secondary region disappeared, confirming LRS |
| Chrome "Dangerous site" | Chrome blocked my blob URL. It flags the shared `blob.core.windows.net` domain | Verified privacy through settings instead: container access level Private, account blob anonymous access Disabled |

- Created the private container `lab3-files` and uploaded a test file.

**Lab 4: Budget alert and cleanup**
- Created `budget-lab4` on the subscription: £5 per month, alert at 80% of actual cost and 100% of forecasted cost, sent by email.

Problems and fixes:
| Problem | What happened | Fix |
|---|---|---|
| No Budgets menu | Cost Management was open at billing-account scope | Opened the subscription from Subscriptions and used its Budgets page |
| Create blocked | "Action group or at least one email address is required" | Added an alert email address |
| Duplicate email error | "Unique email address is required" | Removed the address I had entered twice |

- Cleanup: deleted `vm-lab2_group`, `rg-lab2` and `rg-lab3`. Only `NetworkWatcherRG` remains, which Azure creates automatically and which is free.

Cleanup problems and fixes:
| Problem | What happened | Fix |
|---|---|---|
| Delete button missing | It does not appear in the Resource groups list or its row menu | Opened each resource group and used the toolbar. Zooming the browser out revealed the button |
| Deleted groups still listed | The list does not refresh itself | Clicked Refresh. Opening a deleted group showed "Resource not found", confirming it was gone |
| Upgrade button | The portal header has an Upgrade button for the trial | Did not click it, so the subscription cannot start charging |

- Current cost at the time of cleanup: about £0.10.

**Publishing**
- Created the GitHub repository and uploaded the README.

---

## What I would do next
- Add a Conditional Access and MFA lab (optional).
- Repeat Lab 2 using the Azure CLI instead of the portal.
- Add the portfolio link to my CV and LinkedIn.
