Выполнил: Смирнов Н.М.

Группа: ЭВТ-70

Игровой движок: Unity 2021.3.15

Название работы: разработка механики 2D рогалика

Ход работы

⦁ Выполнение работы

Создание игрового проекта 2D рогалик

⦁ Создал анимации

 ![image](https://user-images.githubusercontent.com/119409903/205115136-d7da3d46-f6b5-4d2f-90c9-d404858658d8.png)

Рис. 33.2 Анимации

⦁ Создал префабы

 ![image](https://user-images.githubusercontent.com/119409903/205116814-b3a92368-1401-432f-920d-f0a95b71868e.png)

Рис. 33.2 Префабы


⦁ Создал 2 сцены


 ![image](https://user-images.githubusercontent.com/119409903/205116873-98089663-cfc2-4ca8-b50b-a02b825064ef.png)


Рис. 33.2 Сцены

⦁ Сцена MainMenu

 ![image](https://user-images.githubusercontent.com/119409903/205116943-8876aa3a-d3d5-4860-a0c2-57a8ccdeea4d.png)

Рис. 33.2 Иерархия сцены MainMenu

⦁ Сцена Level 01

 ![image](https://user-images.githubusercontent.com/119409903/205117058-e5f6cd7b-f063-42ce-b4cc-5e07fae91a8b.png)

Рис. 33.2 Иерархия сцены Level 01

⦁ Создал скрипт MainMenu, который отвечает за действия при нажатии кнопок в главном меню.

Листинг MainMenu.cs

использование UnityEngine;

с помощью UnityEngine.Управление сценой;


основное меню общедоступного класса: единоличное поведение
{
 общедоступная панель настроек игрового объекта;

 публичная пустая игра запуска()
    {
 Менеджер сцен.Сцена загрузки(1);
    }

 открытые настройки public void()
    {
 Панель настроек.SetActive(значение true);
    }

 общедоступные пустые настройки закрытия()
    {
 Панель настроек.SetActive(ложь);
    }

 публичный недействительный выход из игры()
    {
 Application.Quit();
    }
}

⦁ Создал скрипт CameraFollowPlayer, перемещающий камеру за игроком.

Листинг CameraFollowPlayer.cs

использование UnityEngine;


камера публичного класса, подписывающийся игрок : единоличное поведение
{
 публичный трансформатор;

 публичное сглаживание с плавающей запятой;

 смещение публичного Вектора3;

 аннулирует исправленное обновление()
    {
 если (игрок != null)
        {
 Vector3 newPosition = Vector3.Lerp(transform.position, player.transform.position + смещение, сглаживание);

 transform.position = новое положение;
        }
    }
}

⦁	Создал скрипт CurrencyPickup, отвечающий за подбор монет и самоцветов игроком.

Листинг CurrencyPickup.cs

using UnityEngine;


public class CurrencyPickup : MonoBehaviour
{
    public enum PickupObject { COIN, GEM }

 общедоступный объект PickupObject currentObject;

 общедоступное количество входов;

 частная пустота при запуске enter2d (столкновение Collider2D)
    {
 если (collision.name == "Игрок")
        {
 Статистика игроков.Статистика игроков.Добавить валюту(это);

 Уничтожить (игровой объект);

        }
    }
}

⦁ Создал скрипт Enemy, отвечающий за поведение врага при лечении, получении урона и смерти.

Листинг Enemy.cs

использование UnityEngine;

использование UnityEngine.UI;


общественный классовый враг: единоличное поведение
{
 общественное здравоохранение;

 публичное размещение maxHealth;

 публичный игровой объект монета;

 общедоступная панель здоровья игрового объекта;

 общественный Слайдер healthBarSlider healthBarSlider;

 запуск частной недействительности()
    {
 здоровье = максимальное здоровье;
    }

 публичный недействительный ущерб от сделки (урон от поплавка)
    {
 Панель здоровья.Установить активный(true);

 здоровье -= урон;

 Проверить смерть();

        healthBarSlider.value = CalculateHealthPercentage();
    }

    public void HealCharacter(float heal)
    {
        health += heal;

        CheckOverheal();

        healthBarSlider.value = CalculateHealthPercentage();
    }

    public void CheckOverheal()
    {
        if (health > maxHealth)

            health = maxHealth;
    }

    private void CheckDeath()
    {
        if (health <= 0)
        {
            Instantiate(coin, transform.position, Quaternion.identity);

            Destroy(gameObject);
        }
    }

    private float CalculateHealthPercentage()
    {
        return health / maxHealth;
    }
}

⦁	Создал скрипт EnemyAttack, ищущий игрока по компоненту.

Листинг EnemyAttack.cs

using UnityEngine;

public class EnemyAttack : MonoBehaviour
{
    protected GameObject player;

    public virtual void Start()
    {
        player = FindObjectOfType<PlayerMovement>().gameObject;
    }
}

⦁	Создал скрипт TestEnemyShooting, отвечающий за стрельбу врага по игроку.

Листинг TestEnemyShooting.cs

using System.Collections;

using UnityEngine;

public class TestEnemyShooting : EnemyAttack
{
    public GameObject projectile;

    public float minDamage;

    public float maxDamage;

    public float projectileForce;

    public float cooldown;

    public override void Start()
    {
        base.Start();

        StartCoroutine(ShootPlayer());
    }

    IEnumerator ShootPlayer()
    {
        yield return new WaitForSeconds(cooldown);

        if (player != null)
        {
            GameObject spell = Instantiate(projectile, transform.position, Quaternion.identity);

            Vector2 myPos = transform.position;

            Vector2 targetPos = player.transform.position;

            Vector2 direction = (targetPos - myPos).normalized;

            spell.GetComponent<Rigidbody2D>().velocity = direction * projectileForce;

            spell.GetComponent<TestEnemyProjectile>().damage = Random.Range(minDamage, maxDamage);

            StartCoroutine(ShootPlayer());
        }
    }
}

⦁	Создал скрипт TestEnemyProjectile, наносящий урон игроку при столкновении с ним.

Листинг TestEnemyProjectile.cs

using UnityEngine;

public class TestEnemyProjectile : MonoBehaviour
{
    public float damage;

    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.tag != "Enemy")

            if (collision.tag == "Player")

                PlayerStats.playerStats.DealDamage(damage);

 Уничтожить (игровой объект);
    }
}

⦁ Создал скрипт EnemySpawner, создающий врагов в случайной точке сцены.

Листинг EnemySpawner.cs

использование системы.Коллекции;

использование системы.Коллекции.Универсальный;

использование UnityEngine;

враг публичного класса: единоличное поведение
{
 общедоступный список<GameObject> Врагов = новый список<GameObject>();

 общедоступный показатель появления с плавающей точкой;

 частные значения с плавающей запятой x, y;

 частный вектор3 spawnPos;

 запуск частной недействительности()
    {
 Запустить процедуру (SpawnTestEnemy());
    }

 IEnumerator порождает Testenemy()
    {
 x = Случайный.Диапазон (-1, 1);

 y = Случайный.Диапазон (-1, 1);

 spawnPos.x += x;

 спаунПос.y += y;
 Создание экземпляра (Враги[0], spawnPos, Quaternion.identity);

 yield возвращает новые секунды ожидания (spawnRate);

 Запустить процедуру (SpawnTestEnemy());
    }
}
⦁ Создал скрипт FloatToPlayer, притягивающий предмет к игроку.

Листинг FloatToPlayer.cs

использование UnityEngine;

public class FloatToPlayer : MonoBehaviour
{
    public GameObject player;

    public float speed;

    void Start()
    {
        player = GameObject.Find("Player");
    }

    void Update()
    {
        if (player != null)

            transform.position = Vector3.MoveTowards(transform.position, player.transform.position, speed * Time.deltaTime);
    }
}

⦁	Создал скрипт PlayerMovement, отвечающий за передвижение игрока и его анимацию.

Листинг PlayerMovement.cs

using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    public float dashRange;

    public float speed;

    private Vector2 direction;

    private Animator animator;

    private Vector2 targetPos;

    private enum Facing { UP, DOWN, LEFT, RIGHT }

    private Facing FacingDir = Facing.DOWN;

    private void Start()
    {
        animator = GetComponent<Animator>();
    }

    void Update()
    {
        TakeInput();
        Move();
    }

    private void Move()
    {
        transform.Translate(direction * speed * Time.deltaTime);

        if (direction.x != 0 || direction.y != 0)

            SetAnimatorMovement(direction);
        else
            animator.SetLayerWeight(1, 0);
    }
    private void TakeInput()
    {
        direction = Vector2.zero;

        if (Input.GetKey(KeyCode.W))
        {
            direction += Vector2.up;

            FacingDir = Facing.UP;
        }
        if (Input.GetKey(KeyCode.A))
        {
            direction += Vector2.left;

            FacingDir = Facing.LEFT;
        }
        if (Input.GetKey(KeyCode.S))
        {
            direction += Vector2.down;

            FacingDir = Facing.DOWN;
        }
        if (Input.GetKey(KeyCode.D))
        {
            direction += Vector2.right;

            FacingDir = Facing.RIGHT;
        }
        if (Input.GetKeyDown(KeyCode.Space))
        {
            Vector2 currentPos = transform.position;

            targetPos = Vector2.zero;

            if (FacingDir == Facing.UP)

                targetPos.y = 1;

 иначе, если (FacingDir == Облицовка.ВНИЗ)

 targetPos.y = -1;

 иначе, если (FacingDir == Облицовка.СЛЕВА)

 targetPos.x = -1;

 иначе, если (FacingDir == Облицовка.ПРАВИЛЬНО)

 targetPos.x = 1;

 трансформируйте.Перевести(targetPos * dashRange);
        }
    }

 частное пустое SetAnimatorMovement(направление Вектора2)
    {
 аниматор.Набор веса (1, 1);

 аниматор.Установить плавучесть ("xDir", direction.x);

 аниматор.SetFloat("yDir", направление.y);
    }
}

⦁ Создал скрипт PlayerStats, содержащий информацию о показателях игрока и их изменение.

Листинг PlayerStats.cs

использование UnityEngine;

использование UnityEngine.UI;

статистика игроков публичного класса : единоличное поведение
{
 общедоступные статические данные игроков PlayerStats;

 публичный игрок игрового объекта;

 общедоступный текст healthText;

 общественный слайдер healthSlider;

 общественное здравоохранение;

 публичное размещение maxHealth;

 публичные монеты int;

    public int gems;

    public Text coinsValue;

    public Text gemsValue;

    private void Awake()
    {
        if (playerStats != null)

            Destroy(playerStats);
        else
            playerStats = this;

        DontDestroyOnLoad(this);
    }

    private void Start()
    {
        health = maxHealth;

        SetHealthUI();
    }

    public void DealDamage(float damage)
    {
        health -= damage;

        CheckDeath();

        SetHealthUI();
    }

    public void HealCharacter(float heal)
    {
        health += heal;

        CheckOverheal();

        SetHealthUI();
    }

    private void SetHealthUI()
    {
        healthSlider.value = CalculateHealthPercentage();

        healthText.text = Mathf.Ceil(health).ToString() + " / " + Mathf.Ceil(maxHealth).ToString();
    }

    public void CheckOverheal()
    {
        if (health > maxHealth)

            health = maxHealth;
    }

    private void CheckDeath()
    {
        if (health <= 0)
        {
            health = 0;

            Destroy(player);
        }
    }

 вычислить значение с плавающей запятой()
    {
 вернуть здоровье / maxHealth;
    }

 общедоступная пустая добавленная валюта (CurrencyPickup currency)
    {
 если (currency.currentObject == CurrencyPickup.PickupObject.МОНЕТА)
        {
 монеты += количество валюты.;

 Стоимость монет.text = "Золото: " + монеты;
        }
 else, если (currency.currentObject == CurrencyPickup.PickupObject.GEM)
        {
 драгоценные камни += количество валюты.;

 gemsValue.text = "Драгоценные камни: " + драгоценные камни;
        }
    }
}

⦁ Создал скрипт TestProjectile, отвечающий за нанесение урона врагам при соприкосновении.

Листинг TestProjectile.cs

использование UnityEngine;

тестовый проект публичного класса: однонаправленное поведение
{
 повреждение публичного поплавка;

 частная пустота при запуске enter2d (столкновение Collider2D)
    {
 если (collision.name != "Игрок")

 если (столкновение.GetComponent<Враг>() != null)

 столкновение.Получить компонент<Враг>().Нанести урон (урон);

 Уничтожить (игровой объект);
    }
}

⦁ Создал скрипт TestSpell, отвечающий за стрельбу игрока снарядами в направлении курсора.

Листинг TestSpell.cs

using UnityEngine;

public class TestSpell : MonoBehaviour
{
    public GameObject projectile;

    public float minDamage;

    public float maxDamage;

    public float projectileForce;

    void Update()
    {
        if (Input.GetMouseButtonDown(1))
        {
 Заклинание игрового объекта = Создание экземпляра (снаряд, трансформация.позиция, Кватернион.идентичность);

 Vector2 mousePos = Camera.main.Экранная точка (ввод.Положение мыши);

 Вектор2 myPOS = преобразование.положение;

 Вектор2 направление = (mousePos - myPOS).нормализованный;

 заклинание.Получаем компонент<Rigidbody2D>().скорость = направление * Проективная сила;

 заклинание.GetComponent<TestProjectile>().повреждение = Случайное.Диапазон (минимальный ущерб, максимальный ущерб);
        }
    }
}
⦁ Вывод

В ходе проделанной работы были приобретены навыки в разработке механики 2D рогалика.
