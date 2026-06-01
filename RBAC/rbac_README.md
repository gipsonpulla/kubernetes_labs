#Use RBAC to Limit User Permissions

* Kubernetes is a powerful container orchestration platform, but with great
  power comes the need for robust security measures. In this challenge you will
  focus on understanding a fundamental aspect of Kubernetes security: RBAC
  (Role-Based Access Control).

* RBAC in Kubernetes is used to control the actions users or groups can perform
  over specific resources within the cluster. To implement RBAC in Kubernetes, you
  will make use of different resources:

    Roles and ClusterRoles: They define a set of rules. These rules are purely
    additive, meaning they are no deny rules (you will only be able to allow access
    but not deny). Roles are tied to a namespace whereas ClusterRoles are not
    namespace specific, they are cluster-scoped.

    RoleBindings and ClusterRoleBindings: They bind a specific Role or ClusterRoles
    to users, groups or service accounts to grant them the permissions defined in
    the roles. 

*   In this challenge you will create a Role that will allow user1 full control over
    Pods in the challenge2 namespace. You will also create a ClusterRole to allow
    user2 to list Pods in the entire cluster, but user2 will not be able to modify
    or create new Pods.







