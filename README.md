import React, { useEffect, useRef, useState } from 'react';

export default function Game() {
  const canvasRef = useRef(null);

  const [move, setMove] = useState({ x: 0, y: 0 });
  const [shoot, setShoot] = useState(false);
  const [ulti, setUlti] = useState(false);

  const [brawler, setBrawler] = useState(0);
  const [enemyHp, setEnemyHp] = useState(100);

  const brawlers = [
    { name: '⚡ Speed', speed: 5, dmg: 6, ulti: 20 },
    { name: '🛡 Tank', speed: 2, dmg: 12, ulti: 35 },
    { name: '🎯 Sniper', speed: 3, dmg: 18, ulti: 45 },
    { name: '🔥 Mage', speed: 3, dmg: 10, ulti: 50 },
    { name: '🥷 Ninja', speed: 6, dmg: 8, ulti: 25 }
  ];

  useEffect(() => {
    const canvas = canvasRef.current;
    const ctx = canvas.getContext('2d');

    const player = { x: 100, y: 200, size: 20 };
    const enemy = { x: 550, y: 200, size: 20, dir: 1 };

    const bullets = [];

    const loop = () => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      ctx.fillStyle = "#111";
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      const current = brawlers[brawler];

      // déplacement
      player.x += move.x * current.speed;
      player.y += move.y * current.speed;

      // tir
      if (shoot) {
        bullets.push({ x: player.x, y: player.y });
        setShoot(false);
      }

      // ulti
      if (ulti) {
        setEnemyHp(h => Math.max(0, h - current.ulti));
        setUlti(false);
      }

      // ennemi bouge
      enemy.y += enemy.dir * 2;
      if (enemy.y < 50 || enemy.y > 350) enemy.dir *= -1;

      // joueur
      ctx.fillStyle = "purple";
      ctx.beginPath();
      ctx.arc(player.x, player.y, player.size, 0, Math.PI * 2);
      ctx.fill();

      // ennemi
      ctx.fillStyle = "red";
      ctx.beginPath();
      ctx.arc(enemy.x, enemy.y, enemy.size, 0, Math.PI * 2);
      ctx.fill();

      // balles
      bullets.forEach((b, i) => {
        b.x += 7;
        ctx.fillStyle = "cyan";
        ctx.fillRect(b.x, b.y, 10, 5);

        const dx = b.x - enemy.x;
        const dy = b.y - enemy.y;

        if (Math.sqrt(dx*dx + dy*dy) < enemy.size) {
          bullets.splice(i, 1);
          setEnemyHp(h => Math.max(0, h - current.dmg));
        }
      });

      requestAnimationFrame(loop);
    };

    loop();
  }, [move, shoot, ulti, brawler]);

  return (
    <div style={{ textAlign: "center", color: "white", background: "black", height: "100vh" }}>
      <h1>Brawl Stars Beta ⚡ batioXx</h1>

      <p>Perso: {brawlers[brawler].name}</p>

      <canvas ref={canvasRef} width={700} height={400} style={{ border: "2px solid purple" }} />

      {/* choix perso */}
      <div>
        {brawlers.map((b, i) => (
          <button key={i} onClick={() => setBrawler(i)}>
            {b.name}
          </button>
        ))}
      </div>

      {/* contrôles */}
      <div style={{ display: "flex", justifyContent: "center", gap: 20, marginTop: 20 }}>

        {/* BLEU déplacement */}
        <div style={{ background: "blue", padding: 10 }}>
          <button onMouseDown={()=>setMove({x:0,y:-1})} onMouseUp={()=>setMove({x:0,y:0})}>⬆️</button><br/>
          <button onMouseDown={()=>setMove({x:-1,y:0})} onMouseUp={()=>setMove({x:0,y:0})}>⬅️</button>
          <button onMouseDown={()=>setMove({x:1,y:0})} onMouseUp={()=>setMove({x:0,y:0})}>➡️</button><br/>
          <button onMouseDown={()=>setMove({x:0,y:1})} onMouseUp={()=>setMove({x:0,y:0})}>⬇️</button>
        </div>

        {/* ROUGE attaque */}
        <button onClick={()=>setShoot(true)} style={{ background: "red", width: 80, height: 80, borderRadius: "50%" }}>
          🔴
        </button>

        {/* JAUNE ulti */}
        <button onClick={()=>setUlti(true)} style={{ background: "yellow", width: 80, height: 80, borderRadius: "50%" }}>
          ⭐
        </button>

      </div>
    </div>
  );
}
