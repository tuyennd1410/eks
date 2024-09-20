# 1️⃣ Create DB Security Group

- Go to Amazon VPC / Security Groups

- Create Security Group name: `db-sg`

- Inbound rule:

  - Type: Postgresql

  - Custom: 192.168.0.0/16

# 2️⃣ Create DB Subnet Group

- Go to Amazon RDS Console \ `Subnet groups`

- Choose **Create DB subnet group**

  - Name: `vietaws5-dbsub`

  - Description: `subnets for rds`

  - VPC: `eksctl-vietaws5-cluster...`

  - AZ: 1a & 1b

  - Subnets: Choose private subnets (You should check on `Subnets` to see
    Private or Public subnets)

  - Click **Create**

# 3️⃣ Create RDS Postgres

1. Go to RDS Console

2. Choose `Postgresql`

3. `Templates` \ `Free Tier`

4. **DB instance identifier** \ Enter `students`

5. **Credentials Setting**

- Master Username: `dbadmin`

- Master Password: `dbadmin123`

6. **DB Instance Class**

- Burstable Class: `db.t3.micro`

7. **Storage Type**: `gp3`

8. Choose VPC: `vietaws5-vpc`

9. DB Subnet Group: `vietaws5-dbsub`

10. Uncheck **Turn on Performance Insights**

11. 🚀 Click on **Additional Configuration**

- Initial Database name: `vietaws`

- Uncheck **Enable Backup**

12. Click **Create database**

# 4️⃣ Test connectivity

```
# Create psql client
kubectl run psql-client \
--labels="app=students-app,env=prod" \
--rm --tty -i --restart='Never' \
--namespace default --image bitnami/postgresql \
--env="PGPASSWORD=dbadmin123" \
--command -- psql --host rds-psql -U dbadmin -d vietaws

# Create db
CREATE DATABASE vietaws;

# List DB
\l

# Choose DB
\c vietaws

# check table

\dt

# create table

CREATE TABLE students( id SERIAL PRIMARY KEY, name VARCHAR(50) NOT NULL, score
INT);

# insert new records

INSERT INTO students(id, name, score) VALUES (DEFAULT, 'Viet Tran', 5),
(DEFAULT, 'Raymond', 7), (DEFAULT, 'Alex', 6), (DEFAULT, 'Tommy', 7), (DEFAULT,
'David', 8), (DEFAULT, 'Kumishida', 6), (DEFAULT, 'Jenni', 8), (DEFAULT,
'Kevin', 7), (DEFAULT, 'Mina',9);

# Select
SELECT * FROM students;
```

# 5️⃣ Create Pod to connect psql

```
# Update ExternalName for rds
Manifest: manifests/01-rds.psql.yaml
  externalName: students.culc3cntmkhf.ap-southeast-1.rds.amazonaws.com

# Update secret if any. Default password: dbadmin123

# Deploy App & Ingress
kubectl apply -f manifests
```

# 6️⃣ Verify

```
# Pod
kubectl get pods -w

# Deployment
kubectl get deployments

# Describe Pod
kubectl describe pods students-deployment-xxx

# Access Pod
kubectl exec -it students-deployment-xxx -- sh
  $app# ls
  $app# nslookup rds-psql

# Access Logs
kubectl logs -f students-deployment-xxx

# Access Web
https://rds.eks.vietaws.com/db

=> ✅ Green: Pod is connected to RDS Postgres

```

# 7️⃣ Change ExternalName for RDS

```
# Update rds externalname to a random name. Test Failed connection
Eg:  externalName: students.culc3cntmkhf-999.ap-southeast-1.rds.amazonaws.com

# Apply
kubectl apply -f manifests

# Verify web
https://rds.eks.vietaws.com/db

=> ⛔️ Red: PostgreSQL is NOT connected to RDS Postgres

```

# 8️⃣ Clean Up

```
# Delete RDS Postgres
Approach 1: Using AWS Management Console. Uncheck Final Snapshot
Approach 2: Using CLI

aws rds delete-db-instance --db-instance-identifier students --skip-final-snapshot --profile eks

# Delete App
kubectl delete -f manifests
```
