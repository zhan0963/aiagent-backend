# user model design

model User {
  id        String   @id @default(auto()) @map("_id") @db.ObjectId
  email     String   @unique
  password  String
  nickname  String?
  role      Role     @default(USER)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  sessions  Session[]
}

model Session {
  id        String   @id @default(auto()) @map("_id") @db.ObjectId
  sessionId String   @unique
  userId    String   @db.ObjectId
  user      User     @relation(fields: [userId], references: [id])
  expiresAt DateTime
  data      Json?
  createdAt DateTime @default(now())
}

enum Role {
  USER
  ADMIN
}