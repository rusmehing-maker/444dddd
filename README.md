<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nova Portfolio | Digital future</title>
    
    <!-- Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap" rel="stylesheet">
    
    <!-- Tailwind CSS (Direct CDN) -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- React & Libraries (Direct CDN) -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://unpkg.com/framer-motion@10.16.4/dist/framer-motion.js"></script>
    <script src="https://unpkg.com/lucide@latest"></script>

    <script>
      tailwind.config = {
        theme: {
          extend: {
            fontFamily: {
              sans: ['Inter', 'sans-serif'],
            }
          }
        }
      }
    </script>
    
    <style>
      html { scroll-behavior: smooth; }
      body { 
        background-color: #020617; 
        color: #f8fafc; 
        margin: 0;
        overflow-x: hidden;
      }
      .particles-canvas { 
        position: fixed; 
        top: 0; 
        left: 0; 
        width: 100%; 
        height: 100%; 
        pointer-events: none; 
        z-index: 0; 
      }
      #root {
        position: relative;
        z-index: 10;
      }
      .gradient-hero {
        background: radial-gradient(circle at 50% 50%, rgba(37, 99, 235, 0.1) 0%, transparent 50%);
      }
      /* Стили для иконок lucide при рендеринге через браузер */
      .lucide {
        display: inline-block;
        vertical-align: middle;
      }
    </style>
</head>
<body>
    <!-- Background Animation Layer -->
    <canvas id="particles" class="particles-canvas"></canvas>

    <div id="root"></div>

    <script type="text/babel">
      const { useState, useEffect, useRef } = React;

      // Безопасный доступ к Framer Motion из глобального объекта
      const motion = window.framerMotion ? window.framerMotion.motion : {
        div: (props) => <div {...props}>{props.children}</div>,
        h1: (props) => <h1 {...props}>{props.children}</h1>,
        section: (props) => <section {...props}>{props.children}</section>,
        p: (props) => <p {...props}>{props.children}</p>,
        header: (props) => <header {...props}>{props.children}</header>
      };

      // --- Исправленный компонент иконки ---
      const LucideIcon = ({ name, size = 24, className = "" }) => {
        const iconRef = useRef(null);
        
        useEffect(() => {
          if (iconRef.current && window.lucide) {
            // Форматируем имя иконки из CamelCase в kebab-case для data-lucide
            const kebabName = name
              .replace(/([a-z0-9])([A-Z])/g, '$1-$2')
              .toLowerCase();
            
            // Очищаем и создаем элемент-заполнитель
            iconRef.current.innerHTML = `<i data-lucide="${kebabName}"></i>`;
            
            // Вызываем метод создания иконок для конкретного контейнера
            window.lucide.createIcons({
              icons: window.lucide.icons,
              attrs: {
                width: size,
                height: size,
                class: className,
                'stroke-width': 2,
                stroke: 'currentColor'
              },
              container: iconRef.current
            });
          }
        }, [name, size, className]);

        return <span ref={iconRef} className={`inline-flex items-center justify-center ${className}`}></span>;
      };

      // --- Анимация частиц ---
      const initParticles = () => {
        const canvas = document.getElementById('particles');
        if (!canvas) return;
        const ctx = canvas.getContext('2d');
        let particles = [];
        
        const resize = () => {
          canvas.width = window.innerWidth;
          canvas.height = window.innerHeight;
        };

        const create = () => {
          particles = [];
          for(let i=0; i<80; i++) {
            particles.push({
              x: Math.random() * canvas.width,
              y: Math.random() * canvas.height,
              s: Math.random() * 2 + 0.5,
              vx: (Math.random() - 0.5) * 0.5,
              vy: (Math.random() - 0.5) * 0.5
            });
          }
        };

        const draw = () => {
          ctx.clearRect(0,0,canvas.width, canvas.height);
          ctx.fillStyle = 'rgba(148, 163, 184, 0.15)';
          particles.forEach(p => {
            p.x += p.vx; p.y += p.vy;
            if(p.x < 0) p.x = canvas.width; if(p.x > canvas.width) p.x = 0;
            if(p.y < 0) p.y = canvas.height; if(p.y > canvas.height) p.y = 0;
            ctx.beginPath();
            ctx.arc(p.x, p.y, p.s, 0, Math.PI*2);
            ctx.fill();
          });
          requestAnimationFrame(draw);
        };

        window.addEventListener('resize', () => {
          resize();
          create();
        });
        resize();
        create();
        draw();
      };

      // --- Основной компонент приложения ---
      const App = () => {
        const [scrolled, setScrolled] = useState(false);
        const [formState, setFormState] = useState('idle');

        useEffect(() => {
          initParticles();
          const onScroll = () => setScrolled(window.scrollY > 50);
          window.addEventListener('scroll', onScroll);
          return () => window.removeEventListener('scroll', onScroll);
        }, []);

        const projects = [
          { title: "Quantum Hub", type: "Web App", img: "https://images.unsplash.com/photo-1620641788421-7a1c342ea42e?w=800&q=80" },
          { title: "Nova Design", type: "Visual Arts", img: "https://images.unsplash.com/photo-1639762681485-074b7f938ba0?w=800&q=80" },
          { title: "Crypto Flow", type: "FinTech", img: "https://images.unsplash.com/photo-1547658719-da2b51169166?w=800&q=80" },
          { title: "Task Master", type: "System", img: "https://images.unsplash.com/photo-1555066931-4365d14bab8c?w=800&q=80" }
        ];

        return (
          <div className="flex flex-col min-h-screen">
            {/* Header */}
            <nav className={`fixed top-0 w-full z-50 transition-all duration-300 ${scrolled ? 'bg-slate-950/80 backdrop-blur-md py-4 border-b border-white/5' : 'py-8'}`}>
              <div className="max-w-7xl mx-auto px-6 flex justify-between items-center">
                <a href="#" className="text-2xl font-black tracking-tighter">NOVA<span className="text-blue-500">.</span></a>
                <div className="hidden md:flex gap-10">
                  {['about', 'work', 'contact'].map(id => (
                    <a key={id} href={`#${id}`} className="text-xs font-bold uppercase tracking-widest text-slate-400 hover:text-white transition-colors">
                      {id === 'about' ? 'Обо мне' : id === 'work' ? 'Работы' : 'Контакты'}
                    </a>
                  ))}
                </div>
              </div>
            </nav>

            {/* Hero */}
            <header className="min-h-screen flex items-center justify-center px-6 gradient-hero pt-20">
              <div className="max-w-4xl text-center">
                <div className="inline-flex items-center gap-2 px-4 py-2 bg-blue-500/10 border border-blue-500/20 rounded-full text-blue-400 text-[10px] font-black uppercase tracking-widest mb-10">
                  <span className="w-2 h-2 rounded-full bg-blue-500 animate-pulse"></span>
                  Ready for new projects
                </div>
                <h1 className="text-6xl md:text-9xl font-black tracking-tighter mb-8 leading-[0.9]">
                  UNLIMITED <br /> <span className="text-transparent bg-clip-text bg-gradient-to-r from-blue-500 to-indigo-400">CREATIVITY.</span>
                </h1>
                <p className="text-slate-400 text-lg md:text-xl max-w-2xl mx-auto mb-12">
                  Я превращаю сложные идеи в элегантные цифровые решения. Дизайн, код и стратегия в одном флаконе.
                </p>
                <div className="flex flex-col sm:flex-row justify-center gap-6">
                  <a href="#work" className="px-12 py-5 bg-white text-slate-950 font-black rounded-2xl hover:scale-105 transition-all">СМОТРЕТЬ РАБОТЫ</a>
                  <a href="#contact" className="px-12 py-5 border border-white/10 rounded-2xl hover:bg-white/5 transition-all font-bold">СВЯЗАТЬСЯ</a>
                </div>
              </div>
            </header>

            {/* About */}
            <section id="about" className="py-32 px-6">
              <div className="max-w-7xl mx-auto grid lg:grid-cols-2 gap-20 items-center">
                <div className="relative">
                  <div className="aspect-[4/5] rounded-[48px] overflow-hidden grayscale hover:grayscale-0 transition-all duration-700 shadow-2xl">
                    <img src="https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=800&q=80" className="w-full h-full object-cover" />
                  </div>
                  <div className="absolute -bottom-10 -right-10 p-12 bg-blue-600 rounded-[40px] hidden xl:block shadow-2xl">
                    <div className="text-5xl font-black mb-1 text-white">5+</div>
                    <div className="text-[10px] font-bold uppercase tracking-widest opacity-80 text-white">Years EXP</div>
                  </div>
                </div>
                <div>
                  <div className="text-blue-500 font-black uppercase tracking-[0.3em] text-[10px] mb-8">ОБО МНЕ</div>
                  <h2 className="text-4xl md:text-6xl font-black mb-8 leading-tight">Создаю интерфейсы, <br/> которые вдохновляют.</h2>
                  <p className="text-slate-400 text-lg mb-10 leading-relaxed italic border-l-2 border-blue-500 pl-8">
                    "Я верю, что хороший дизайн — это не только картинка. Это инструмент, который решает проблемы бизнеса и делает жизнь пользователей проще."
                  </p>
                  <div className="grid grid-cols-2 gap-8">
                    <div className="p-8 bg-white/5 border border-white/10 rounded-[32px]">
                      <LucideIcon name="Code" className="text-blue-500 mb-4" />
                      <h4 className="font-bold text-white uppercase text-xs tracking-widest">Разработка</h4>
                    </div>
                    <div className="p-8 bg-white/5 border border-white/10 rounded-[32px]">
                      <LucideIcon name="Figma" className="text-blue-500 mb-4" />
                      <h4 className="font-bold text-white uppercase text-xs tracking-widest">Дизайн</h4>
                    </div>
                  </div>
                </div>
              </div>
            </section>

            {/* Portfolio */}
            <section id="work" className="py-32 px-6 bg-slate-900/10">
              <div className="max-w-7xl mx-auto">
                <div className="flex flex-col md:flex-row justify-between items-end mb-24 gap-10">
                  <h2 className="text-5xl md:text-8xl font-black">ПОРТФОЛИО.</h2>
                  <p className="text-slate-500 max-w-sm mb-4">Выборочные проекты, демонстрирующие мой подход к работе.</p>
                </div>
                <div className="grid md:grid-cols-2 gap-12 lg:gap-20">
                  {projects.map((p, i) => (
                    <div key={i} className="group relative">
                      <div className="aspect-video rounded-[32px] overflow-hidden mb-8 border border-white/5 shadow-2xl">
                        <img src={p.img} className="w-full h-full object-cover transition-transform duration-700 group-hover:scale-110 grayscale group-hover:grayscale-0" />
                      </div>
                      <div className="flex justify-between items-end px-2">
                        <div>
                          <p className="text-blue-500 font-black text-[10px] uppercase tracking-widest mb-2">{p.type}</p>
                          <h3 className="text-3xl font-black">{p.title}</h3>
                        </div>
                        <LucideIcon name="ArrowUpRight" size={32} className="text-slate-700 group-hover:text-white transition-colors" />
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            </section>

            {/* Contact */}
            <section id="contact" className="py-32 px-6">
              <div className="max-w-4xl mx-auto rounded-[60px] bg-gradient-to-br from-white/10 to-transparent border border-white/10 p-12 md:p-24 text-center">
                <h2 className="text-5xl md:text-8xl font-black mb-8">CONTACT.</h2>
                <p className="text-slate-400 text-lg mb-16 max-w-lg mx-auto">Оставьте запрос, и я свяжусь с вами в течение 24 часов.</p>
                <form 
                  onSubmit={e => {e.preventDefault(); setFormState('sent')}} 
                  className="space-y-6 max-w-xl mx-auto"
                >
                  <input placeholder="Name" required className="w-full bg-slate-900 border border-white/5 rounded-2xl px-6 py-5 focus:border-blue-500 transition-colors outline-none text-white" />
                  <input type="email" placeholder="Email" required className="w-full bg-slate-900 border border-white/5 rounded-2xl px-6 py-5 focus:border-blue-500 transition-colors outline-none text-white" />
                  <textarea placeholder="Message" rows="4" className="w-full bg-slate-900 border border-white/5 rounded-2xl px-6 py-5 focus:border-blue-500 transition-colors outline-none text-white"></textarea>
                  <button className="w-full py-6 rounded-2xl bg-blue-600 font-black text-xl hover:bg-blue-500 transition-all shadow-xl shadow-blue-600/20 text-white">
                    {formState === 'idle' ? 'SEND MESSAGE' : 'THANK YOU!'}
                  </button>
                </form>
              </div>
            </section>

            {/* Footer */}
            <footer className="py-20 px-6 border-t border-white/5 mt-auto">
              <div className="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center gap-12">
                <div className="text-2xl font-black text-white">NOVA<span className="text-blue-500">.</span></div>
                <div className="flex gap-12">
                  <a href="#" className="font-bold text-slate-500 hover:text-white transition-colors">Telegram</a>
                  <a href="#" className="font-bold text-slate-500 hover:text-white transition-colors">Github</a>
                </div>
                <p className="text-slate-700 text-[10px] font-black uppercase tracking-[0.4em]">© 2026 NOVA Digital</p>
              </div>
            </footer>
          </div>
        );
      };

      const root = ReactDOM.createRoot(document.getElementById('root'));
      root.render(<App />);
    </script>
</body>
</html>
