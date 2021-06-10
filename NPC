using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AI;
using UnityEngine.SceneManagement;


public class Boss : MonoBehaviour 
{
    public GameObject player;
    public GameObject boss;
    private NavMeshAgent nav;
    private Animator anim;
    private string state = "idle";
    private bool alive = true;
    public Transform eyes;
    private float wait= 0f;
    private bool highAlert = false;
    private float alertness = 20f;
    public float distance;
    public float remaindistance;
    public float stopdistance;
    public bool foo = false;
    public int attackTrigger;
    public int isAttacking;
    public ParticleSystem ps;
    public int bossHealth = 20;
    public SimpleHealthBar bossBar;
    public RectTransform healthPanelRect;
    public GameObject healthBarPrefab;
    private int test = 0;
    private AudioSource maudio;
    private float timer;

    // Start is called before the first frame update
    void Start()
    {
        timer = 0;
        maudio = GetComponent<AudioSource>();
        nav = GetComponent<NavMeshAgent>();
        anim = GetComponent<Animator>();
        nav.speed = 2f;
        anim.speed = 1.2f;
    }

    private void GenerateBossHealthBar(Transform bb)
    {
        GameObject bossBarobject = Instantiate(healthBarPrefab) as GameObject;
        bossBarobject.GetComponent<BossBarScript>().SetHealthBarData(bb, healthPanelRect);
        bossBarobject.transform.SetParent(healthPanelRect, false);

    }
    
    // Update is called once per frame
    void Update()
    {
        timer += Time.deltaTime;
        bossBar.UpdateBar(bossHealth, 20);

        if (attackTrigger == 1)
        {
            if (isAttacking == 0)
            {
                StartCoroutine(EnemyDamage());
            }
        }

        //anim.SetFloat("Velocity", nav.velocity.magnitude);
        if (alive)
        {
            anim.SetFloat("Velocity", nav.velocity.magnitude);
            
            //idle
            if(state == "idle")
            {
               
                Vector3 randomPos = Random.insideUnitSphere * alertness;
                NavMeshHit navHit;
                NavMesh.SamplePosition(transform.position + randomPos, out navHit, 20f, NavMesh.AllAreas);

                //Debug.Log("idle1");
                if(highAlert)
                {
                    //Debug.Log("idle2");
                    NavMesh.SamplePosition(player.transform.position + randomPos, out navHit, 20f, NavMesh.AllAreas);
                    alertness += 5f;
                    
                    if(alertness > 20f)
                    {
                        highAlert = false;
                        nav.speed = 2f;
                        anim.speed = 1.2f;
                    }
                }

                nav.SetDestination(navHit.position);
                state = "walk";
            }

            //walk
            if(state == "walk")
            {
                // Debug.Log("walk");
                if (nav.remainingDistance <= nav.stoppingDistance && !nav.pathPending)
                {
                    state = "search";
                    wait = 1f;
                    //StartCoroutine(walkwait());
                }
            }

            //search
            if(state == "search")
            {
                //Debug.Log("search");
                //checkSight();
                if (wait>0f)
                {
                    wait -= Time.deltaTime;
                    transform.Rotate(0f, 120f * Time.deltaTime, 0f);
                }
                else
                {
                    state = "idle";
                }
            }
            
            //run
            if(state == "chase")
            {
                //Debug.Log("CHASE");
                //nav.destination = player.transform.position;
                nav.SetDestination(player.transform.position);
                //loose sight of player
                
                checkSight();
                distance = Vector3.Distance(transform.position, player.transform.position);
                
                if (distance > 20f || foo == false)
                {
                    //Debug.Log(foo);
                    //Debug.Log("FINALLY");
                    state = "hunt";
                }
            }
            
            //hunt
            if (state == "hunt")
            {
                //Debug.Log("hunt");
                //if (nav.remainingDistance <=nav.stoppingDistance && !nav.pathPending)
                {
                    state = "search";
                    wait = 1f;
                    highAlert = true;
                    alertness = 5f;
                    //checkSight();
                }
            }
            //nav.SetDestination(player.transform.position);

            if(test==1)
            {
                state = "chase";
                nav.speed = 4f;
                anim.speed = 1.2f;
                test = 0;
            }
        }
    }
    
    public void checkSight()
    {
        if(alive)
        {
            Debug.Log("1");
            RaycastHit hit;
            
            if(Physics.Linecast(eyes.position, player.transform.position, out hit))
            {
                //print("hit " + hit.collider.gameObject.name);
                if (hit.collider.gameObject.name == "EnemyTrigger")
                {
                    Debug.Log("2");
                    foo = true;
                    //Debug.Log("hi2");
                    if(state != "kill")
                    {
                        state = "chase";
                        nav.speed = 4f;
                        anim.speed = 1.2f;
                    }
                }
                else if(hit.collider.gameObject.name != "EnemyTrigger")
                {
                    foo = false;
                }
            }
        }
    }

    public void BossDamage(int damageAmount)
    {
        test = 1;
        bossHealth -= damageAmount;
        StartCoroutine(HealthBarFunction());
        
        if (bossHealth <= 0)
        {
            alive = false;
            StartCoroutine(DieFunction());
        }
    }
    IEnumerator HealthBarFunction()
    {
        healthBarPrefab.SetActive(true);
        yield return new WaitForSeconds(5f);
        healthBarPrefab.SetActive(false);
    }

    IEnumerator DieFunction()
    {
        anim.SetBool("Dead", true);
        yield return new WaitForSeconds(3f);
        Analytics.timerSum += timer;
        boss.SetActive(false);
        SceneCounter.sceneNum = 6;
        GlobalHealth.playerHealth = 10;
        SceneManager.LoadScene(6);
    }

    IEnumerator HitFunction()
    {
        anim.SetBool("Hit", false);
        anim.SetBool("Hit", true);
        yield return new WaitForSeconds(3f);
        anim.SetBool("Hit", false);
    }

    IEnumerator EnemyDamage()
    {
        isAttacking = 1;
        maudio.Play();
        // yield return new WaitForSeconds(0.9f);
        //screenTint.SetActive(true);
        GlobalHealth.playerHealth -= 1;
        ps.Play();
        yield return new WaitForSeconds(3f);
        //screenTint.SetActive(false);
        //  yield return new WaitForSeconds(1f);
        ps.Stop();
        isAttacking = 0; 
    }
    
    private void OnTriggerEnter(Collider other)
    {
        if (other.gameObject.name == "FPSController" && foo == true)
        {
            anim.SetBool("Attacking", true);
            attackTrigger = 1;
        }
    }

    private void OnTriggerExit(Collider other)
    {
        if (other.gameObject.name == "FPSController")
        {
            anim.SetBool("Attacking", false);
            attackTrigger = 0;
        }
    }


}
