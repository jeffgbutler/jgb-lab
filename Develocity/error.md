```
2024-12-03 14:17:19,675 INFO c.g.i.c.s.V1_20230616_1200__create_gradle_enterprise_bucket {} Starting V1_20230616_1200__create_gradle_enterprise_bucket step.
2024-12-03 14:17:19,919 INFO c.g.i.c.s.V1_20230616_1200__create_gradle_enterprise_bucket {} Bucket 'gradle-enterprise' already exists, creation not required
2024-12-03 14:17:19,919 INFO c.g.i.c.s.V1_20230616_1200__create_gradle_enterprise_bucket {} Step V1_20230616_1200__create_gradle_enterprise_bucket done.
2024-12-03 14:17:19,919 INFO c.g.i.c.s.V2_20230616_1200__create_geapp_account {} Starting V2_20230616_1200__create_geapp_account step.
2024-12-03 14:17:20,986 ERROR Main {} Error migrating Internal Object Storage config: mac check in GCM failed org.bouncycastle.crypto.InvalidCipherTextException: mac check in GCM failed
     at org.bouncycastle.crypto.modes.GCMBlockCipher.doFinal(Unknown Source)
     at io.minio.admin.Crypto.decrypt(Crypto.java:190)
     at io.minio.admin.MinioAdminClient.listUsers(MinioAdminClient.java:288)
     at com.gradle.enterprise.internalobjectstorageconfig.j.a(SourceFile:9)
     at com.gradle.internalobjectstorage.configinitializer.steps.V2_20230616_1200__create_geapp_account.a(SourceFile:22)
     at com.gradle.internalobjectstorage.configinitializer.steps.b.a(SourceFile:13)
     at com.gradle.internalobjectstorage.configinitializer.a.a(SourceFile:55)
     at com.gradle.internalobjectstorage.configinitializer.a.a(SourceFile:30)
"gradle-enterprise-app" in pod "gradle-enterprise-app-55ff7ffc5d-sq2ll" is waiting to start: PodInitializing for develocity/gradle-enterprise-app-55ff7ffc5d-sq2ll (gradle-enterprise-app)
     at com.gradle.internalobjectstorage.configinitializer.MainKt.b(SourceFile:42)
     at com.gradle.internalobjectstorage.configinitializer.MainKt.a(SourceFile:19)
     at com.gradle.internalobjectstorage.configinitializer.MainKt.main(SourceFile)
```


cat /proc/sys/kernel/random/entropy_avail: 256 on Proxmox
