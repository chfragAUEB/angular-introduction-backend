# Εισαγωγή στο framework NestJS

## 0. Δημιουργία του module users και σύνδεση με την υπηρεσία Atlas

```
❯ nest generate module user
CREATE src/user/user.module.ts (81 bytes)
UPDATE src/app.module.ts (308 bytes)

❯ nest generate controller user
CREATE src/user/user.controller.spec.ts (478 bytes)
CREATE src/user/user.controller.ts (97 bytes)
UPDATE src/user/user.module.ts (166 bytes)

❯ nest generate service user
CREATE src/user/user.service.spec.ts (446 bytes)
CREATE src/user/user.service.ts (88 bytes)
UPDATE src/user/user.module.ts (240 bytes)
```

Εγκατάσταση του `@nestjs/mongoose`:

```
npm i @nestjs/mongoose mongoose
```

Δημιουργία του αρχείου `user.schema.ts` στο κατάλογο `users`:

```typescript
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { HydratedDocument } from 'mongoose';
@Schema()
export class User {
  @Prop({ type: String, required: true })
  givenName: string;
  @Prop({ type: String, required: true })
  surName: string;
  @Prop({ type: Number, required: true })
  age: string;
  @Prop({ type: String, required: true, unique: true })
  email: string;
  @Prop({ type: String, required: true })
  address: string;
  @Prop({ type: String, default: '' })
  photoURL: string;
}
export type UserDocument = HydratedDocument<User>;
export const UserSchema = SchemaFactory.createForClass(User);
```

Χρήση του σχήματος στο `user module`:

```typescript
import { Module } from '@nestjs/common';
import { UserController } from './user.controller';
import { UserService } from './user.service';
import { MongooseModule } from '@nestjs/mongoose';
import { User, UserSchema } from './user.schema';

@Module({
  imports: [
    MongooseModule.forFeature([{ name: User.name, schema: UserSchema }]),
  ],
  controllers: [UserController],
  providers: [UserService],
})
export class UserModule {}
```

Σύνδεση στην υπηρεσία Atlas για τον καθορισμό του connection string:

![](constring.png)

Χρήση του connection string στο app.module.ts:

```typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { MongooseModule } from '@nestjs/mongoose';
import { UserModule } from './user/user.module';

@Module({
  imports: [
    MongooseModule.forRoot(
      'mongodb+srv://dbadmin:FEyz99503wXyi5K2@cluster0.okry00y.mongodb.net/CodingFactory4?retryWrites=true&w=majority',
    ),
    UserModule,
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

Έναρξη του development server:

```
❯ nest start
[Nest] 46866  - 10/31/2023, 10:50:47 AM     LOG [NestFactory] Starting Nest application...
[Nest] 46866  - 10/31/2023, 10:50:47 AM     LOG [InstanceLoader] MongooseModule dependencies initialized +24ms
[Nest] 46866  - 10/31/2023, 10:50:47 AM     LOG [InstanceLoader] UserModule dependencies initialized +1ms
[Nest] 46866  - 10/31/2023, 10:50:47 AM     LOG [InstanceLoader] AppModule dependencies initialized +0ms
[Nest] 46866  - 10/31/2023, 10:50:48 AM     LOG [InstanceLoader] MongooseCoreModule dependencies initialized +863ms
[Nest] 46866  - 10/31/2023, 10:50:48 AM     LOG [InstanceLoader] MongooseModule dependencies initialized +9ms
[Nest] 46866  - 10/31/2023, 10:50:48 AM     LOG [RoutesResolver] AppController {/}: +15ms
[Nest] 46866  - 10/31/2023, 10:50:48 AM     LOG [RouterExplorer] Mapped {/, GET} route +4ms
[Nest] 46866  - 10/31/2023, 10:50:48 AM     LOG [RoutesResolver] UserController {/user}: +1ms
[Nest] 46866  - 10/31/2023, 10:50:48 AM     LOG [NestApplication] Nest application successfully started +3ms
```

H βάση και η collection δημιουργήθηκαν:

![](dbcreated.png)

Για να μην κάνουμε push στο GitHub username και password δημιουργούμε ένα αρχείο `src/.env` και εκεί τοποθετούμε τα credentials για την υπηρεσία Atlas:

```
MONGODB_URI=mongodb+srv://dbadmin:FEyz99503wXyi5K2@cluster0.okry00y.mongodb.net/CodingFactory4?retryWrites=true&w=majority
```

**Δεν ξεχνάμε να προσθέσουμε το `.env` στο αρχείο `.gitignore`**

```
.env
# compiled output
/dist
/node_modules
...
```

Εγκαθιστούμε το πακέτο `@nestjs/config`:

```
npm i @nestjs/config
```

Προσθέτουμε στο `app.module.ts`:

```typescript
...
import { ConfigModule } from '@nestjs/config';
...
@Module({
  imports: [
    ConfigModule.forRoot(),
    MongooseModule.forRoot(process.env.MONGODB_URI),
...
```

## -1. Εγκατάσταση και πρώτη εκτέλεση

```
❯ npm i -g @nestjs/cli

added 37 packages, removed 5 packages, and changed 243 packages in 24s

56 packages are looking for funding
  run `npm fund` for details

~/Coding Factory/preparation took 24s
❯ nest new angular-introduction-backend
⚡  We will scaffold your app in a few seconds..

? Which package manager would you ❤️  to use? npm
CREATE angular-introduction-backend/.eslintrc.js (663 bytes)
CREATE angular-introduction-backend/.prettierrc (51 bytes)
CREATE angular-introduction-backend/README.md (3340 bytes)
CREATE angular-introduction-backend/nest-cli.json (171 bytes)
CREATE angular-introduction-backend/package.json (1969 bytes)
CREATE angular-introduction-backend/tsconfig.build.json (97 bytes)
CREATE angular-introduction-backend/tsconfig.json (546 bytes)
CREATE angular-introduction-backend/src/app.controller.ts (274 bytes)
CREATE angular-introduction-backend/src/app.module.ts (249 bytes)
CREATE angular-introduction-backend/src/app.service.ts (142 bytes)
CREATE angular-introduction-backend/src/main.ts (208 bytes)
CREATE angular-introduction-backend/src/app.controller.spec.ts (617 bytes)
CREATE angular-introduction-backend/test/jest-e2e.json (183 bytes)
CREATE angular-introduction-backend/test/app.e2e-spec.ts (630 bytes)

✔ Installation in progress... ☕

🚀  Successfully created project angular-introduction-backend
👉  Get started with the following commands:

$ cd angular-introduction-backend
$ npm run start


                          Thanks for installing Nest 🙏
                 Please consider donating to our open collective
                        to help us maintain this package.


               🍷  Donate: https://opencollective.com/nest
```
