## 課題
`repository.ts`に`save`メソッドがあり、引数に単数か複数の`entity`を受け取るようになっている。
ただ、`save`メソッドを呼び出した場合の返り値の型が`User | User[]`となるため、呼び出し側で型の特定をする必要がある。
`save`メソッド側で`entity`の型に合わせて、返り値の型を動的に特定できるようにしたい。

```ts
export default class Repository<E> {
  user: User;
  entityClass: ObjectType<E>;
  entityManager: EntityManager;

  constructor(user: User, entityClass: ObjectType<E>) {
    this.user = user;
    this.entityClass = entityClass;
    this.entityManager = getManager();
  }

  save = async (entity: E | E[], transaction?: EntityManager) => {
    if (transaction) {
      return await transaction.save(entity);
    }
    return await this.entityManager.save(entity);
  };

  //他の処理
}

```

## 仮案
一旦下記のようにメソッド自体を分ける形にしているが、各呼び出し元を修正せずに対応できるのであればそうしたい。

```ts
save = async (entity: E, transaction?: EntityManager): Promise<E> => {
  if (transaction) {
    return await transaction.save(entity);
  }
  return await this.entityManager.save(entity);
};

saveList = async (entities: E[], transaction?: EntityManager): Promise<E[]> => {
  if (transaction) {
    return await transaction.save(entities);
  }
  return await this.entityManager.save(entities);
};
```
