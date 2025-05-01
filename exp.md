# 🔐 Gestion des rôles, permissions et accès (Laravel + Filament)

## 📚 Technologies utilisées
- [Spatie Laravel-Permission](https://spatie.be/docs/laravel-permission)
- [Filament Admin Panel](https://filamentphp.com/)
- [Laravel Authorization (Policies)](https://laravel.com/docs/authorization)

---

## 🧩 Structure de l'autorisation

### 1. 📛 Rôles et permissions (Spatie)

Spatie permet de créer :
- Des **rôles** (ex: `admin`, `auteur`, `user`)
- Des **permissions** (ex: `create blogpost`, `edit event`)
- L’assignation de rôles et permissions aux utilisateurs

#### Exemple d’initialisation
```php
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;

Permission::create(['name' => 'create blogpost']);
Permission::create(['name' => 'edit blogpost']);
Permission::create(['name' => 'delete blogpost']);

$admin = Role::create(['name' => 'admin']);
$auteur = Role::create(['name' => 'auteur']);

$admin->givePermissionTo(['create blogpost', 'edit blogpost', 'delete blogpost']);
$auteur->givePermissionTo(['create blogpost']);
```

#### Attribution à un utilisateur
```php
$user = User::find(1);
$user->assignRole('admin');
```

---

### 2. ⚙️ Intégration avec Filament

Dans Filament, on peut restreindre les actions d'une Resource :

```php
public static function canViewAny(): bool
{
    return auth()->user()->can('view blogpost');
}

public static function canCreate(): bool
{
    return auth()->user()->can('create blogpost');
}
```

➡️ Docs : https://filamentphp.com/docs/3.x/panels/authorization

---

### 3. 🧠 Policies Laravel

Laravel permet de centraliser les règles d’autorisation via des **Policies**.

#### Création d'une Policy
```bash
php artisan make:policy BlogPostPolicy --model=BlogPost
```

#### Exemple de méthode
```php
public function create(User $user)
{
    return $user->can('create blogpost');
}
```

#### Enregistrement
```php
protected \$policies = [
    BlogPost::class => BlogPostPolicy::class,
];
```

➡️ Docs : https://laravel.com/docs/authorization#writing-policies

---

### 4. 🧪 Utilisation dans Blade ou Controller

```blade
@can('create', App\Models\BlogPost::class)
    <a href="{{ route('blogposts.create') }}">Créer un blogpost</a>
@endcan
```

---

## 📌 Résumé

| Élément            | Outil/technique             | Description                              |
|--------------------|-----------------------------|------------------------------------------|
| Gestion des rôles  | Spatie Laravel-Permission   | Gérer rôles/permissions via la DB        |
| Interface admin    | Filament Admin              | Formulaires + autorisations              |
| Accès par action   | Laravel Policies            | Encapsule les règles dans des classes    |
| Sécurisation Filament | canCreate, canViewAny()   | Contrôle l’accès aux pages dans l’admin  |

