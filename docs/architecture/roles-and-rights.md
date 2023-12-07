### Roles & Rights


Rights and permissions are handled on the level of ***organizations*** and ***spaces***, which are abstractions relating
to customers (organization) and their projects (spaces). Each organization has one or multiple spaces, and each space
has only one organization it is related to. Permissions are granted via roles for organizations and spaces separately.
Users can be members of multiple organizations as well as multiple spaces by being assigned roles for their respective
level of access.


#### Roles


Individual rights are grouped into roles as follows:


##### organization-Level


###### Information owner (=Owner)

* Approver for the assignment of rights, cf. rights assignment process at the organization level.
  The information owner is a person who is responsible for maintaining the confidentiality of certain information.
  He/she is responsible for the contents of the organization (responsibility in case of legal violations or similar).

The role of the information owner must be filled (by a representative or successor), and it is his responsibility to
ensure that the role of the information owner is filled.


###### Access

* basic-authorization to access data
* in order to access data, dedicated space-permissions are required

###### Admin

* representative of the owner
* is authorized by the owner to perform administrative activities

###### Trustee

* is authorized to handle Dashboards on an organization-leveled basis

##### space-Level


###### Information owner (=Owner)

* Approver for the assignment of rights, cf. rights assignment process at the space level.
  The information owner is a person who is responsible for maintaining the confidentiality of certain information.
  He/she is responsible for the contents of the space (responsibility in case of legal violations or similar).

The role of the information owner must be filled (representative or successor), that the role of the information owner
and the data keeper (trustee) are filled is his responsibility.


###### Supplier

* may create new data
* may read existing data on the loading zone

###### Trustee

* may create new data and edit or delete them

###### User

* may read and download information from the storage

#### A closer Look


Taking the preceding explanations into account, the following authorization-matrix results:

| organization                       |                              | Owner | Admin | Access | Trustee |
| ---------------------------------- | ---------------------------- | ----- | ----- | ------ | ------- |
| Authorization (organization-Level) | Read Members                 | x     | x     |        |         |
|                                    | Get Authorization            | x     | x     |        |         |
|                                    | Edit Members                 | x     | x     |        |         |
|                                    | Edit Authorization           | x     | x     |        |         |
| Authorization (space-Level)        | Read Members                 | x     | x     |        |         |
|                                    | Get Authorization            | x     | x     |        |         |
|                                    | Edit Members                 | x     | x     |        |         |
|                                    | Edit Authorization           | x     | x     |        |         |
| Userrequests (organization-Level)  | Create Userrequest [^1]      |       |       |        |         |
|                                    | List Userrequest [^2]        | x     | x     |        |         |
|                                    | Edit Userrequest             | x     | x     |        |         |
| Userrequests (space-Level)         | Create Userrequest [^1]      |       |       |        |         |
|                                    | List Userrequest [^2]        | x     | x     |        |         |
|                                    | Edit Userrequest             | x     | x     |        |         |
| Owners                             | Get Owners                   | x     | x     | x      | x       |
|                                    | Edit Owners                  | x     |       |        |         |
| organization-Management            | Create organization [^3]     |       |       |        |         |
|                                    | Update organization          | x     | x     |        |         |
|                                    | Get organization             | x     | x     | x      | x       |
| space-Management                   | Get space                    | x     | x     | x [^4] |         |
|                                    | Create space                 | x     | x     |        |         |
|                                    | Edit space                   | x     | x     |        |         |
|                                    | Mark space for deletion [^5] | x     | x     |        |         |
|                                    | Delete Space6 [^6]           |       |       |        |         |
| Dashboards                         | Create Dashboards            |       | x     |        | x       |

| space                          |                               | Owner [^7] | User | Supplier | Trustee |
| ------------------------------ | ----------------------------- | ---------- | ---- | -------- | ------- |
| Authorization (space-Level)    | Read Members                  | x          |      |          |         |
|                                | Get Authorization             | x          |      |          |         |
|                                | Edit Members                  | x          |      |          |         |
|                                | Edit Authorization            | x          |      |          |         |
| Userrequests (space-Level)     | Create Userrequest [^1]       |            |      |          |         |
|                                | List Userrequest [^2]         | x          | x    |          |         |
|                                | Edit Userrequest              | x          | x    |          |         |
| Owners                         | Get Owners                    | x          | x    | x        | x       |
|                                | Edit Owners                   | x          |      |          |         |
| space-Management               | Get space                     | x          | x    | x        | x       |
|                                | Edit space                    | x          |      |          |         |
|                                | Mark space for deletion [^5]  | x          | x    |          |         |
|                                | Delete Space6 [^6]            |            |      |          |         |
| Measurement data (Loadingzone) | Read/Download                 |            |      | x        | x       |
|                                | Edit/Upload                   |            |      | x        | x       |
|                                | Delete                        |            |      | x        | x       |
| Measurement data               | Read/Download                 |            | x    | x        | x       |
|                                | Edit/Upload                   |            |      | x        | x       |
|                                | Delete                        |            |      |          | x       |
| Metadata                       | Read                          |            | x    | x        | x       |
|                                | Edit [^8]                     |            |      |          | x       |
|                                | Delete                        |            |      |          | x       |
| Analysis                       | Create Application-Index      |            |      |          | x       |
|                                | Delete Application-Index [^9] |            |      |          | x       |
|                                | Read/use Analysis             |            | x    | x        | x       |
|                                | Create Analysis [^10]         |            |      |          | x       |

**Special-case: PUBLIC-Confidentiality - besides the standard roles there are roles "org_all_public" and "
spc_all_public", which every user has. These roles authorize read access in the corresponding area, while the
corresponding organization/space-roles are available for write access.**

| organization            |                  | all_public |
| ----------------------- | ---------------- | ---------- |
| organization-Management | Get organization | x          |

| space            |               | all_public |
| ---------------- | ------------- | ---------- |
| space-Management | Get space     | x          |
| Measurement data | Read/Download | x          |
| Metadata         | Read          | x          |


[^1]: Userrequests can be created by any user

[^2]: Every user can list their own UserRequests

[^3]: Creating Organizations is currently only supported for global admins, however we plan to introduce global organization admins for this purpose

[^4]: Read rights only if permissions exist on the corresponding space

[^5]: To avoid unintentional data loss, spaces are not deleted immediately but only after a certain grace period - see [^6]

[^6]: Deleting a space immediately is reserved for global administrators

[^7]: An owner is assigned all space roles at the beginning of his status, which is why he has full access in the space context. This representation therefore only contains those rights that an owner has in his capacity as owner.

[^8]: Since deletion in Opensearch can also be interpreted as "empty overwrite", these permissions are summarized

[^9]: Here the creator of the index must be considered

[^10]: However in order to create a dashboard, "orga-trustee"-permission is required