> From [https://docs.openstack.org/mitaka/user-guide/common/glossary.html](https://docs.openstack.org/mitaka/user-guide/common/glossary.html).
> 
> This glossary offers a list of terms and definitions to define a vocabulary for OpenStack-related concepts.

**availability zone**
An Amazon EC2 concept of an isolated area that is used for fault tolerance. Do not confuse with an OpenStack Compute zone or cell.

**domain**
An Identity API v3 entity. Represents a collection of projects, groups and users that defines administrative boundaries for managing OpenStack Identity entities. On the Internet, separates a website from other sites. Often, the domain name has two or more parts that are separated by dots. For example, [yahoo.com](http://yahoo.com), [usa.gov](http://usa.gov), [harvard.edu](http://harvard.edu), or [mail.yahoo.com](http://mail.yahoo.com). Also, a domain is an entity or container of all DNS-related information containing one or more records.

**external network**
A network segment typically used for instance Internet access.

**host**
A physical computer, not a VM instance (node).

**project**
Projects represent the base unit of “ownership” in OpenStack, in that all resources in OpenStack should be owned by a specific project. In OpenStack Identity, a project must be owned by a specific domain.

**quota**
In Compute and Block Storage, the ability to set resource limits on a per-project basis.
- Nova에서는 per project or user라고 되어있음