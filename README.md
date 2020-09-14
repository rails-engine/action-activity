# action-activity

Social activity

## TODO

```sql
class CreateActivities < ActiveRecord::Migration[6.0]
  def change
    create_table :activities do |t|
      t.string :action, null: false
      t.references :actor, polymorphic: true, null: false
      t.references :target, polymorphic: true
      t.references :second_target, polymorphic: true
      t.jsonb :payload, null: false, default: {}
      t.integer :status, null: false, default: 1

      t.timestamps
    end
    
    create_index :activities, [:actor_type, :actor_id, :status]
    create_index :activities, [:actor_type, :actor_id, :action, :status]
    create_index :activities, [:target_type, :target_id]
    create_index :activities, [:second_target_type, :second_target_id]
  end
end
```

- actor - polymorphic，可以是一个用户，可以是一个群
- action - 动态类型，比如 `follow-user`, `create-comment`, `create-topic`, `join-group`
- target - 触发创建出动态的第一个数据对象（确保存储用于是第一个数据），比如 `create-comment` 场景，target 应该是创建出来的 comment
- second_target - 触发创建动态的次级数据对象，比如 `create-comment` 场景里面，comment 的 target
- payload - 用于存储其他数据，比如 `rename-group`，里面 payload 存储 `{ from: "old-name", to: "new-name" }`，也可以存储更复杂的关联数据，已解决 target, second_target 无法满足的场景。
- status - 用于数据存在审核、软删除的场景，在 target / second_target 等数据状态变更的时候，可以同步更新 activity，这样不用删除。


