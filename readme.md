run:docker-compose up -d

then run: docker exec -it mongo1 mongosh


then run: rs.initiate(
  {
    _id: "rs0",
    members: [
      { _id: 0, host: "mongo1:27017" },
      { _id: 1, host: "mongo2:27017" },
      { _id: 2, host: "mongo3:27017" }
    ]
  }
)

rs.status() --> check status and primary database


uri --- "mongodb://localhost:<primary-port>/<db_name>?replicaSet=rs0&directConnection=true"
