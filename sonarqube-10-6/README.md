# sonarqube-10-6

```text
export POSTGRES_PASSWORD=  # FIXME: Set a non-empty password, lest Postgres not work

# Start the project
podman compose up -d

# Auth as the "sonar" superuser
podman exec -it sonarqube-10-6-postgres psql -U sonar -d sonarqube

# Optional: Copy/restore from dump file, reinstate admin password
podman cp sonarqube-10-6.dump sonarqube-10-6-postgres:/tmp

podman exec -it sonarqube-10-6-postgres pg_restore -v /tmp/sonarqube-10-6.dump -U sonar -d sonarqube --clean

update users set crypted_password='100000$t2h8AtNs1AlCHuLobDjHQTn9XppwTIx88UjqUm4s8RsfTuXQHSd/fpFexAnewwPsO6jGFQUv/24DnO55hY6Xew==',
  salt='k9x9eN127/3e/hf38iNiKwVfaVk=',
  hash_method='PBKDF2',
  reset_password='true',
  user_local='true',
  active='true'
where login='admin';

# Make "Somar way" go bad
insert into quality_gate_conditions (uuid, operator, value_error, created_at, updated_at, metric_uuid, qgate_uuid) (select gen_random_uuid(), operator, value_error, created_at, updated_at, metric_uuid, qgate_uuid from quality_gate_conditions where qgate_uuid in (select uuid from quality_gates where name ='Sonar way'));
```

## References

* [POSTGRES_HOST_AUTH_METHOD](https://github.com/docker-library/docs/blob/master/postgres/README.md#postgres_password)
* [POSTGRES_PASSWORD](https://github.com/docker-library/docs/blob/master/postgres/README.md#postgres_host_auth_method)
