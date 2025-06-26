# flexmerge

[![PyPI version](https://badge.fury.io/py/flexmerge.svg)](https://badge.fury.io/py/flexmerge)
[![Python versions](https://img.shields.io/pypi/pyversions/flexmerge.svg)](https://pypi.org/project/flexmerge/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Tests](https://github.com/yourusername/flexmerge/workflows/Upload%20Python%20Package/badge.svg)](https://github.com/yourusername/flexmerge/actions)
[![Coverage](https://img.shields.io/badge/coverage-95%25-brightgreen.svg)](https://github.com/yourusername/flexmerge)

**flexmerge**は、Pythonで辞書を直感的かつ柔軟にマージするための軽量ライブラリです。複雑な辞書マージロジックを簡単に実装でき、カスタム戦略の登録も可能です。

## 特徴

- 🚀 **シンプルで直感的なAPI** - メソッドチェーンで簡単設定
- 🔧 **柔軟な戦略システム** - リストと辞書の異なるマージ戦略
- 🎯 **カスタム戦略対応** - 独自のマージロジックを簡単に追加
- 📦 **軽量** - 外部依存なし、純粋Python実装
- 🧪 **高品質** - 90%以上のテストカバレッジ
- 🐍 **Python 3.10+対応** - 最新のPython機能を活用

## インストール

パッケージマネージャーを使用してインストールできます：

```bash
pip install flexmerge
```

または、開発版をインストール：

```bash
pip install git+https://github.com/yourusername/flexmerge.git
```

## クイックスタート

### 基本的な辞書マージ

最もシンプルな使用方法：

```python
from flexmerge import Merger

# Mergerインスタンスを作成
merger = Merger()

# 2つの辞書をマージ
dict1 = {"name": "Alice", "skills": ["Python", "JavaScript"]}
dict2 = {"age": 30, "skills": ["React", "Node.js"]}

result = merger.merge(dict1, dict2)
print(result)
# Output: {
#     'name': 'Alice', 
#     'age': 30, 
#     'skills': ['Python', 'JavaScript', 'React', 'Node.js']
# }
```

### 便利関数を使用した簡単マージ

```python
from flexmerge import merge, merge_unique, merge_shallow

# デフォルトマージ（リスト追加、深いマージ）
result = merge(
    {"config": {"debug": True}, "tags": ["dev"]},
    {"config": {"port": 8000}, "tags": ["api"]}
)
print(result)
# Output: {'config': {'debug': True, 'port': 8000}, 'tags': ['dev', 'api']}

# 重複除去マージ
result = merge_unique(
    {"technologies": ["Python", "Docker"]},
    {"technologies": ["Python", "Kubernetes"]}
)
print(result)
# Output: {'technologies': ['Python', 'Docker', 'Kubernetes']}

# 浅いマージ（ネストした辞書を置換）
result = merge_shallow(
    {"database": {"host": "localhost", "port": 5432}},
    {"database": {"host": "prod.example.com"}}
)
print(result)
# Output: {'database': {'host': 'prod.example.com'}}
```

## リスト戦略の詳細

### append戦略（デフォルト）

```python
from flexmerge import Merger

merger = Merger().lists("append")
result = merger.merge(
    {"items": [1, 2, 3]},
    {"items": [4, 5, 6]}
)
print(result)
# Output: {'items': [1, 2, 3, 4, 5, 6]}
```

### prepend戦略

```python
from flexmerge import Merger

merger = Merger().lists("prepend")
result = merger.merge(
    {"priorities": ["medium", "low"]},
    {"priorities": ["high", "urgent"]}
)
print(result)
# Output: {'priorities': ['high', 'urgent', 'medium', 'low']}
```

### unique戦略

```python
from flexmerge import Merger

merger = Merger().lists("unique")
result = merger.merge(
    {"tags": ["python", "web", "api"]},
    {"tags": ["web", "api", "rest", "json"]}
)
print(result)
# Output: {'tags': ['python', 'web', 'api', 'rest', 'json']}
```

### replace戦略

```python
from flexmerge import Merger

merger = Merger().lists("replace")
result = merger.merge(
    {"old_list": [1, 2, 3]},
    {"old_list": [4, 5, 6]}
)
print(result)
# Output: {'old_list': [4, 5, 6]}
```

### keep戦略

```python
from flexmerge import Merger

merger = Merger().lists("keep")
result = merger.merge(
    {"original": ["a", "b"]},
    {"original": ["c", "d"]}
)
print(result)
# Output: {'original': ['a', 'b']}
```

## 辞書戦略の詳細

### deep戦略（デフォルト）

```python
from flexmerge import Merger

merger = Merger().dicts("deep")
result = merger.merge(
    {
        "database": {
            "host": "localhost",
            "port": 5432,
            "credentials": {"user": "admin"}
        }
    },
    {
        "database": {
            "port": 3306,
            "credentials": {"password": "secret"},
            "ssl": True
        }
    }
)
print(result)
# Output: {
#     'database': {
#         'host': 'localhost',
#         'port': 3306,
#         'credentials': {'user': 'admin', 'password': 'secret'},
#         'ssl': True
#     }
# }
```

### shallow戦略

```python
from flexmerge import Merger

merger = Merger().dicts("shallow")
result = merger.merge(
    {"config": {"debug": True, "level": "info"}},
    {"config": {"debug": False}}
)
print(result)
# Output: {'config': {'debug': False}}
```

### replace戦略

```python
from flexmerge import Merger

merger = Merger().dicts("replace")
result = merger.merge(
    {"settings": {"theme": "dark", "lang": "en"}},
    {"settings": {"theme": "light"}}
)
print(result)
# Output: {'settings': {'theme': 'light'}}
```

### keep戦略

```python
from flexmerge import Merger

merger = Merger().dicts("keep")
result = merger.merge(
    {"preferences": {"theme": "dark", "notifications": True}},
    {"preferences": {"theme": "light", "sound": False}}
)
print(result)
# Output: {'preferences': {'theme': 'dark', 'notifications': True}}
```

## 戦略の組み合わせ

```python
from flexmerge import Merger

# リストは重複除去、辞書は深くマージ
merger = Merger().lists("unique").dicts("deep")

config1 = {
    "services": ["web", "database"],
    "database": {
        "host": "localhost",
        "port": 5432,
        "options": {"ssl": True}
    }
}

config2 = {
    "services": ["database", "cache", "queue"],
    "database": {
        "host": "prod.example.com",
        "options": {"pool_size": 10}
    }
}

result = merger.merge(config1, config2)
print(result)
# Output: {
#     'services': ['web', 'database', 'cache', 'queue'],
#     'database': {
#         'host': 'prod.example.com',
#         'port': 5432,
#         'options': {'ssl': True, 'pool_size': 10}
#     }
# }
```

## カスタム戦略

### デコレーターを使用したカスタム戦略

```python
from flexmerge import Merger

merger = Merger()

@merger.list_strategy("sorted_unique")
def sorted_unique_merge(left, right):
    """リストをマージして重複を除去し、ソートする"""
    return sorted(set(left + right))

@merger.dict_strategy("merge_if_compatible")
def merge_if_compatible(left, right):
    """互換性がある場合のみマージ"""
    if left.get("version") == right.get("version"):
        return {**left, **right}
    return left

# カスタム戦略を使用
merger = merger.lists("sorted_unique").dicts("merge_if_compatible")

result = merger.merge(
    {"numbers": [3, 1, 4], "version": "1.0", "name": "app"},
    {"numbers": [1, 5, 9], "version": "1.0", "author": "dev"}
)
print(result)
# Output: {'numbers': [1, 3, 4, 5, 9], 'version': '1.0', 'name': 'app', 'author': 'dev'}
```

### 関数を直接戦略として使用

```python
from flexmerge import Merger

def max_length_list(left, right):
    """長い方のリストを選択"""
    return left if len(left) >= len(right) else right

def sum_numeric_values(left, right):
    """数値の値を合計"""
    result = left.copy()
    for key, value in right.items():
        if key in result and isinstance(result[key], (int, float)) and isinstance(value, (int, float)):
            result[key] += value
        else:
            result[key] = value
    return result

merger = Merger().lists(max_length_list).dicts(sum_numeric_values)

result = merger.merge(
    {"scores": [85, 90, 78], "total": 100, "bonus": 5},
    {"scores": [92, 88], "total": 150, "penalty": -10}
)
print(result)
# Output: {'scores': [85, 90, 78], 'total': 250, 'bonus': 5, 'penalty': -10}
```

## 複数辞書のマージ

```python
from flexmerge import Merger

merger = Merger().lists("unique").dicts("deep")

# デフォルト設定
defaults = {
    "app": {
        "name": "MyApp",
        "version": "1.0.0",
        "features": ["auth", "logging"]
    },
    "database": {
        "host": "localhost",
        "port": 5432
    }
}

# 環境設定
environment = {
    "app": {
        "debug": True,
        "features": ["auth", "debugging"]
    },
    "database": {
        "host": "dev.example.com"
    }
}

# ユーザー設定
user_config = {
    "app": {
        "theme": "dark",
        "features": ["custom_plugin"]
    },
    "database": {
        "timeout": 30
    }
}

final_config = merger.merge(defaults, environment, user_config)
print(final_config)
# Output: {
#     'app': {
#         'name': 'MyApp',
#         'version': '1.0.0',
#         'features': ['auth', 'logging', 'debugging', 'custom_plugin'],
#         'debug': True,
#         'theme': 'dark'
#     },
#     'database': {
#         'host': 'dev.example.com',
#         'port': 5432,
#         'timeout': 30
#     }
# }
```

## Enumを使用した戦略指定

```python
from flexmerge import Merger, BuiltinListStrategies, BuiltinDictStrategies

# Enumを使用して戦略を指定
merger = Merger().lists(BuiltinListStrategies.UNIQUE).dicts(BuiltinDictStrategies.DEEP)

result = merger.merge(
    {"items": [1, 2, 3], "config": {"a": 1}},
    {"items": [3, 4, 5], "config": {"b": 2}}
)
print(result)
# Output: {'items': [1, 2, 3, 4, 5], 'config': {'a': 1, 'b': 2}}
```

## 実世界での使用例

### Webアプリケーション設定の管理

```python
from flexmerge import merge_unique

# ベース設定
base_config = {
    "server": {
        "host": "0.0.0.0",
        "port": 8000,
        "workers": 1
    },
    "database": {
        "engine": "postgresql",
        "host": "localhost",
        "port": 5432
    },
    "features": ["auth", "logging"],
    "middleware": ["cors", "security"]
}

# 本番環境設定
production_config = {
    "server": {
        "workers": 4,
        "ssl": True
    },
    "database": {
        "host": "prod-db.example.com",
        "pool_size": 20
    },
    "features": ["auth", "logging", "monitoring"],
    "middleware": ["cors", "security", "rate_limiting"]
}

# 最終設定
final_config = merge_unique(base_config, production_config)
print(final_config)
```

### APIレスポンスのマージ

```python
from flexmerge import Merger

merger = Merger().lists("unique").dicts("deep")

# ユーザーの基本情報
user_basic = {
    "id": 123,
    "name": "Alice",
    "email": "alice@example.com",
    "roles": ["user"]
}

# ユーザーの詳細情報
user_details = {
    "profile": {
        "bio": "Software developer",
        "skills": ["Python", "JavaScript"]
    },
    "roles": ["user", "developer"],
    "preferences": {
        "theme": "dark",
        "notifications": True
    }
}

# 権限情報
user_permissions = {
    "profile": {
        "skills": ["Python", "JavaScript", "Docker"]
    },
    "roles": ["user", "developer", "admin"],
    "permissions": ["read", "write", "admin"]
}

complete_user = merger.merge(user_basic, user_details, user_permissions)
print(complete_user)
```

### 設定ファイルの階層マージ

```python
from flexmerge import Merger
import json

def load_config_hierarchy():
    """設定ファイルの階層をロードしてマージ"""
    merger = Merger().lists("unique").dicts("deep")
    
    # デフォルト設定
    default_config = {
        "logging": {
            "level": "INFO",
            "handlers": ["console"]
        },
        "features": ["basic"],
        "timeouts": {
            "connection": 30,
            "read": 60
        }
    }
    
    # 環境固有設定
    env_config = {
        "logging": {
            "level": "DEBUG",
            "handlers": ["console", "file"]
        },
        "features": ["basic", "advanced"],
        "timeouts": {
            "connection": 10
        }
    }
    
    # ローカル設定（オプション）
    local_config = {
        "logging": {
            "handlers": ["console", "file", "syslog"]
        },
        "features": ["experimental"],
        "debug": True
    }
    
    return merger.merge(default_config, env_config, local_config)

config = load_config_hierarchy()
print(json.dumps(config, indent=2))
```

## エラーハンドリング

```python
from flexmerge import Merger

merger = Merger()

try:
    # 無効な入力タイプ
    result = merger.merge({"valid": "dict"}, "invalid_input")
except TypeError as e:
    print(f"Error: {e}")
    # Output: Error: Argument 1 is not a dictionary: <class 'str'>

try:
    # 存在しない戦略
    merger.lists("nonexistent_strategy")
except ValueError as e:
    print(f"Error: {e}")
    # Output: Error: Unknown list strategy: nonexistent_strategy
```

## パフォーマンス考慮事項

```python
from flexmerge import Merger
import time

def benchmark_merge_strategies():
    """異なる戦略のパフォーマンスを比較"""
    large_dict1 = {"items": list(range(1000))}
    large_dict2 = {"items": list(range(500, 1500))}
    
    # append戦略
    start = time.time()
    merger_append = Merger().lists("append")
    result_append = merger_append.merge(large_dict1, large_dict2)
    append_time = time.time() - start
    
    # unique戦略
    start = time.time()
    merger_unique = Merger().lists("unique")
    result_unique = merger_unique.merge(large_dict1, large_dict2)
    unique_time = time.time() - start
    
    print(f"Append strategy: {append_time:.4f}s, Result length: {len(result_append['items'])}")
    print(f"Unique strategy: {unique_time:.4f}s, Result length: {len(result_unique['items'])}")

benchmark_merge_strategies()
```

## Mergerのコピーと再利用

```python
from flexmerge import Merger

# ベースとなるMergerを作成
base_merger = Merger().lists("unique").dicts("deep")

# コピーを作成して異なる設定に変更
api_merger = base_merger.copy().lists("append")
config_merger = base_merger.copy().dicts("shallow")

# 元のMergerは変更されない
print(f"Base merger: {base_merger}")
print(f"API merger: {api_merger}")
print(f"Config merger: {config_merger}")
```

## API リファレンス

### Merger クラス

#### コンストラクター

```python
merger = Merger()
```

新しいMergerインスタンスを作成します。デフォルトでは`lists="append"`、`dicts="deep"`戦略を使用します。

#### メソッド

##### `lists(strategy)`

```python
merger = merger.lists("unique")
merger = merger.lists(BuiltinListStrategies.UNIQUE)
merger = merger.lists(custom_function)
```

リストマージ戦略を設定します。

**パラメーター:**
- `strategy`: 戦略名（文字列）、BuiltinListStrategies Enum、または関数

**戻り値:** `Merger` インスタンス（メソッドチェーン用）

##### `dicts(strategy)`

```python
merger = merger.dicts("shallow")
merger = merger.dicts(BuiltinDictStrategies.SHALLOW)
merger = merger.dicts(custom_function)
```

辞書マージ戦略を設定します。

**パラメーター:**
- `strategy`: 戦略名（文字列）、BuiltinDictStrategies Enum、または関数

**戻り値:** `Merger` インスタンス（メソッドチェーン用）

##### `list_strategy(name)`

```python
@merger.list_strategy("custom_name")
def custom_list_merge(left, right):
    return left + right
```

カスタムリスト戦略を登録するデコレーター。

**パラメーター:**
- `name`: 戦略名（文字列）

##### `dict_strategy(name)`

```python
@merger.dict_strategy("custom_name")
def custom_dict_merge(left, right):
    return {**left, **right}
```

カスタム辞書戦略を登録するデコレーター。

**パラメーター:**
- `name`: 戦略名（文字列）

##### `merge(*dicts)`

```python
result = merger.merge(dict1, dict2, dict3)
```

辞書をマージします。

**パラメーター:**
- `*dicts`: マージする辞書（可変長引数）

**戻り値:** マージされた辞書

**例外:**
- `TypeError`: 引数が辞書でない場合

##### `copy()`

```python
new_merger = merger.copy()
```

Mergerインスタンスのコピーを作成します。

**戻り値:** 新しい`Merger`インスタンス

### 便利関数

#### `merge(*dicts, lists="append", dict_strategy="deep")`

```python
from flexmerge import merge

result = merge(dict1, dict2, lists="unique", dict_strategy="shallow")
```

辞書を指定された戦略でマージします。

**パラメーター:**
- `*dicts`: マージする辞書
- `lists`: リスト戦略（デフォルト: "append"）
- `dict_strategy`: 辞書戦略（デフォルト: "deep"）

#### `merge_unique(*dicts)`

```python
from flexmerge import merge_unique

result = merge_unique(dict1, dict2)
```

リストの重複を除去してマージします。

#### `merge_shallow(*dicts)`

```python
from flexmerge import merge_shallow

result = merge_shallow(dict1, dict2)
```

浅いマージを実行します。

### 戦略Enum

#### `BuiltinListStrategies`

```python
from flexmerge import BuiltinListStrategies

# 利用可能な値
BuiltinListStrategies.APPEND
BuiltinListStrategies.PREPEND
BuiltinListStrategies.UNIQUE
BuiltinListStrategies.REPLACE
BuiltinListStrategies.KEEP
```

#### `BuiltinDictStrategies`

```python
from flexmerge import BuiltinDictStrategies

# 利用可能な値
BuiltinDictStrategies.DEEP
BuiltinDictStrategies.SHALLOW
BuiltinDictStrategies.REPLACE
BuiltinDictStrategies.KEEP
```

## 開発

### 開発環境のセットアップ

```bash
# リポジトリをクローン
git clone https://github.com/yourusername/flexmerge.git
cd flexmerge

# 開発依存関係をインストール
pip install -e ".[dev]"

# pre-commitフックをセットアップ
pre-commit install
```

### テストの実行

```bash
# 全テスト実行
pytest

# カバレッジ付きテスト
pytest --cov=flexmerge --cov-report=html

# 特定のテストファイル
pytest tests/test_merger.py

# 特定のテストクラス
pytest tests/test_merger.py::TestMergerBasics

# 特定のテスト関数
pytest tests/test_merger.py::TestMergerBasics::test_simple_merge

# 並列テスト実行
pytest -n auto
```

### コード品質チェック

```bash
# コードフォーマット
black flexmerge/ tests/

# インポートソート
ruff check --fix flexmerge/ tests/

# リンター
ruff check flexmerge/ tests/

# 型チェック
mypy flexmerge/
```

### パッケージビルド

```bash
# ビルド
python -m build

# アップロード（テスト用）
python -m twine upload --repository testpypi dist/*

# アップロード（本番用）
python -m twine upload dist/*
```

## ライセンス

このプロジェクトはMITライセンスの下で公開されています。詳細は[LICENSE](LICENSE)ファイルをご覧ください。

## 貢献

プルリクエストやイシューの報告を歓迎します！貢献する前に、以下を確認してください：

1. **テストが通ること**: `pytest`でテストが成功すること
2. **コードスタイル**: `black`と`ruff`でフォーマットされていること
3. **型チェック**: `mypy`でエラーがないこと
4. **新機能にはテスト**: 新しい機能には対応するテストを含めること
5. **ドキュメント更新**: 必要に応じてREADMEやdocstringを更新すること

### 貢献の手順

```bash
# 1. フォークしてクローン
git clone https://github.com/yourusername/flexmerge.git

# 2. ブランチを作成
git checkout -b feature/new-feature

# 3. 変更を実装
# ... コードを編集 ...

# 4. テストを実行
pytest

# 5. コードスタイルをチェック
black flexmerge/ tests/
ruff check flexmerge/ tests/

# 6. コミットしてプッシュ
git commit -m "Add new feature"
git push origin feature/new-feature

# 7. プルリクエストを作成
```

## 変更履歴

### v0.1.0 (2024-XX-XX)
- 初回リリース
- 基本的な辞書マージ機能
- 複数のリスト・辞書戦略（append, prepend, unique, replace, keep, deep, shallow）
- カスタム戦略対応（デコレーターと直接関数）
- 便利関数の提供（merge, merge_unique, merge_shallow）
- Enumを使用した戦略指定
- 90%以上のテストカバレッジ
- 型ヒント対応
- 包括的なドキュメント

## サポート

質問やサポートが必要な場合は、以下のリソースをご利用ください：

- **GitHub Issues**: バグレポートや機能リクエスト
- **GitHub Discussions**: 一般的な質問や議論
- **Email**: [your.email@example.com](mailto:your.email@example.com)

## 関連プロジェクト

- [deepmerge](https://github.com/toumorokoshi/deepmerge) - 別の辞書マージライブラリ
- [mergedeep](https://github.com/clarketm/mergedeep) - 深いマージに特化したライブラリ
- [dictdiffer](https://github.com/inveniosoftware/dictdiffer) - 辞書の差分を計算するライブラリ