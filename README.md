import { useEffect, useState } from "react";

const ADMIN = { email: "admin@barbearia.com", senha: "123456" };

// Logo clássica de barbearia
const logoUrl = "https://www.shutterstock.com/image-vector/classic-barbershop-logo-design-featuring-260nw-2653718273.jpg";

// SUAS 4 FOTOS REAIS NA GALERIA
const cortes = [
  "https://i.imgur.com/kY4yAIR.png",
  "https://i.imgur.com/WmYeKIT.png",
  "https://i.imgur.com/vv0pl9w.png",
  "https://i.imgur.com/ZaeVX4J.png"
];

export default function App() {
  const [tela, setTela] = useState("login");
  const [email, setEmail] = useState("");
  const [senha, setSenha] = useState("");
  const [nome, setNome] = useState("");
  const [servico, setServico] = useState("");
  const [data, setData] = useState("");
  const [hora, setHora] = useState("");

  const [agendamentos, setAgendamentos] = useState(() => {
    const saved = localStorage.getItem("agendamentos");
    return saved ? JSON.parse(saved) : [];
  });

  useEffect(() => {
    localStorage.setItem("agendamentos", JSON.stringify(agendamentos));
  }, [agendamentos]);

  const handleLogin = (e) => {
    e.preventDefault();
    if (!email.trim() || !senha) {
      alert("Preencha email e senha!");
      return;
    }
    if (email.trim() === ADMIN.email && senha === ADMIN.senha) {
      setTela("barbeiro");
    } else {
      setTela("cliente");
    }
    setEmail("");
    setSenha("");
  };

  const horarioOcupado = () => {
    return agendamentos.some((a) => a.data === data && a.hora === hora);
  };

  const handleAgendar = (e) => {
    e.preventDefault();
    if (!nome.trim() || !servico || !data || !hora) {
      alert("Preencha todos os campos!");
      return;
    }
    if (horarioOcupado()) {
      alert("Horário já ocupado! Escolha outro.");
      return;
    }

    setAgendamentos([
      ...agendamentos,
      { nome: nome.trim(), servico, data, hora, status: "Agendado" },
    ]);

    setNome("");
    setServico("");
    setData("");
    setHora("");
    alert("Agendamento realizado com sucesso! 💈");
  };

  const concluir = (index) => {
    const novos = [...agendamentos];
    novos[index].status = "Concluído";
    setAgendamentos(novos);
  };

  const cancelar = (index) => {
    if (window.confirm("Excluir este agendamento?")) {
      setAgendamentos(agendamentos.filter((_, i) => i !== index));
    }
  };

  const logout = () => setTela("login");

  // Animações CSS (leves e suaves)
  const animacoesCSS = `
    @keyframes fadeInUp {
      from { opacity: 0; transform: translateY(30px); }
      to { opacity: 1; transform: translateY(0); }
    }
    @keyframes slideUp {
      from { opacity: 0; transform: translateY(50px); }
      to { opacity: 1; transform: translateY(0); }
    }
    @keyframes expandLine {
      from { width: 0; }
      to { width: 100%; }
    }
    @keyframes pulseButton {
      0% { transform: scale(1); }
      50% { transform: scale(1.05); }
      100% { transform: scale(1); }
    }
    .animate-fadeInUp { animation: fadeInUp 0.8s ease-out forwards; }
    .animate-slideUp { animation: slideUp 0.8s ease-out forwards; }
    .animate-expandLine { animation: expandLine 1s ease-out forwards; }
    .animate-pulseButton { animation: pulseButton 2s ease-in-out infinite; }
  `;

  const Background = () => (
    <div className="fixed inset-0 -z-10 flex items-center justify-center pointer-events-none opacity-10">
      <img src={logoUrl} alt="Logo fundo" className="max-w-full max-h-full" />
    </div>
  );

  const GaleriaCortes = () => (
    <div className="my-16 px-4">
      <h2 className="text-4xl font-bold text-white text-center mb-10 animate-fadeInUp">
        NOSSOS TRABALHOS
      </h2>
      <div className="grid grid-cols-2 md:grid-cols-4 gap-6">
        {cortes.map((src, i) => (
          <div key={i} className="animate-slideUp" style={{ animationDelay: `${i * 0.2}s` }}>
            <img
              src={src}
              alt="Trabalho R.F Barbearia"
              className="rounded-2xl object-cover h-64 w-full border-4 border-red-700 shadow-xl hover:scale-105 hover:shadow-2xl transition duration-500"
            />
          </div>
        ))}
      </div>
    </div>
  );

  const Footer = () => (
    <div className="mt-16 pt-8 border-t-4 border-red-700 text-center text-gray-300 px-4 pb-8">
      <p className="text-2xl font-bold text-white mb-6 animate-fadeInUp">FORMAS DE PAGAMENTO</p>
      <div className="flex justify-center gap-12 text-5xl mb-8">
        <div className="animate-pulseButton">💳<p className="text-sm mt-2">Cartão</p></div>
        <div className="animate-pulseButton" style={{ animationDelay: '0.3s' }}>💵<p className="text-sm mt-2">Dinheiro</p></div>
        <div className="animate-pulseButton" style={{ animationDelay: '0.6s' }}>🔑<p className="text-sm mt-2">Pix</p></div>
      </div>
      <p className="text-2xl font-bold text-white mb-4 animate-fadeInUp">HORÁRIO DE FUNCIONAMENTO</p>
      <div className="space-y-2 text-xl">
        <p>Terça a Sexta: 09h às 19h</p>
        <p>Sábado: 08h às 19h</p>
        <p>Domingo: 08h às 12h</p>
        <p className="text-red-500 font-bold">Segunda: Fechado</p>
      </div>
      <img src={logoUrl} alt="R.F Barbearia" className="w-48 mx-auto mt-10 opacity-80 animate-fadeInUp" />
    </div>
  );

  const Servicos = () => (
    <div className="my-16 px-4">
      <h2 className="text-4xl font-bold text-white text-center mb-10 animate-fadeInUp">NOSSOS SERVIÇOS</h2>
      <div className="grid md:grid-cols-3 gap-8">
        {[
          { icon: "✂️", title: "Corte Clássico" },
          { icon: "🪒", title: "Barba Completa" },
          { icon: "💈", title: "Corte + Barba" }
        ].map((serv, i) => (
          <div
            key={i}
            className="bg-gray-900 border-4 border-red-700 rounded-2xl p-8 text-center hover:-translate-y-4 hover:shadow-2xl transition duration-500 animate-slideUp shadow-xl"
            style={{ animationDelay: `${i * 0.3}s` }}
          >
            <div className="text-7xl mb-4 animate-pulseButton">{serv.icon}</div>
            <h3 className="text-2xl font-bold text-white">{serv.title}</h3>
          </div>
        ))}
      </div>
    </div>
  );

  return (
    <>
      <style>{animacoesCSS}</style>
      <div className="min-h-screen bg-black text-white relative">
        <Background />

        {tela === "login" && (
          <div className="p-8 flex flex-col items-center">
            <div className="w-full max-w-md bg-gray-900 border-4 border-red-700 rounded-3xl p-10 shadow-2xl animate-fadeInUp">
              <h1 className="text-5xl font-bold text-center mb-8 animate-fadeInUp">
                R.F <span className="text-red-600">BARBEARIA</span>
              </h1>
              <div className="w-32 h-1 bg-blue-600 mx-auto mb-8 animate-expandLine"></div>
              <p className="text-center text-gray-400 mb-10 text-lg animate-fadeInUp">Tradição e estilo desde sempre</p>
              <form onSubmit={handleLogin} className="space-y-6">
                <input type="email" placeholder="Email" value={email} onChange={(e) => setEmail(e.target.value)} className="w-full p-4 rounded-xl bg-gray-800 border border-gray-700 focus:border-red-600 text-lg animate-fadeInUp" style={{ animationDelay: '0.3s' }} required />
                <input type="password" placeholder="Senha" value={senha} onChange={(e) => setSenha(e.target.value)} className="w-full p-4 rounded-xl bg-gray-800 border border-gray-700 focus:border-red-600 text-lg animate-fadeInUp" style={{ animationDelay: '0.5s' }} required />
                <button className="w-full bg-red-700 hover:bg-red-600 font-bold py-4 rounded-xl text-xl animate-pulseButton animate-fadeInUp" style={{ animationDelay: '0.7s' }}>
                  Entrar
                </button>
              </form>
            </div>
            <GaleriaCortes />
            <Footer />
          </div>
        )}

        {tela === "cliente" && (
          <div className="p-8">
            <div className="max-w-4xl mx-auto bg-gray-900 border-4 border-red-700 rounded-3xl p-10 shadow-2xl animate-fadeInUp">
              <h1 className="text-5xl font-bold text-center mb-8 animate-fadeInUp">
                AGENDE SEU <span className="text-red-600">HORÁRIO</span>
              </h1>
              <div className="w-40 h-1 bg-blue-600 mx-auto mb-8 animate-expandLine"></div>
              <p className="text-center text-gray-400 mb-10 text-lg animate-fadeInUp">Corte perfeito no seu tempo</p>
              <form onSubmit={handleAgendar} className="grid md:grid-cols-2 gap-6 mb-10">
                <input placeholder="Seu nome" value={nome} onChange={(e) => setNome(e.target.value)} className="p-4 rounded-xl bg-gray-800 border border-gray-700 focus:border-red-600 text-lg animate-fadeInUp" style={{ animationDelay: '0.2s' }} required />
                <select value={servico} onChange={(e) => setServico(e.target.value)} className="p-4 rounded-xl bg-gray-800 border border-gray-700 focus:border-red-600 text-lg animate-fadeInUp" style={{ animationDelay: '0.4s' }} required>
                  <option value="">Selecione o serviço</option>
                  <option value="Corte">✂️ Corte Clássico</option>
                  <option value="Barba">🪒 Barba Completa</option>
                  <option value="Corte + Barba">💈 Corte + Barba</option>
                </select>
                <input type="date" value={data} onChange={(e) => setData(e.target.value)} min={new Date().toISOString().split("T")[0]} className="p-4 rounded-xl bg-gray-800 border border-gray-700 focus:border-red-600 text-lg animate-fadeInUp" style={{ animationDelay: '0.6s' }} required />
                <input type="time" value={hora} onChange={(e) => setHora(e.target.value)} min="08:00" max="20:00" className="p-4 rounded-xl bg-gray-800 border border-gray-700 focus:border-red-600 text-lg animate-fadeInUp" style={{ animationDelay: '0.8s' }} required />
                <button className="md:col-span-2 bg-red-700 hover:bg-red-600 font-bold py-4 rounded-xl text-xl animate-pulseButton animate-fadeInUp" style={{ animationDelay: '1s' }}>
                  💈 Agendar Agora
                </button>
              </form>
              <button onClick={logout} className="w-full bg-gray-800 hover:bg-gray-700 py-4 rounded-xl font-bold text-lg animate-fadeInUp" style={{ animationDelay: '1.2s' }}>
                Sair
              </button>
            </div>
            <GaleriaCortes />
            <Servicos />
            <Footer />
          </div>
        )}

        {tela === "barbeiro" && (
          <div className="p-8">
            <div className="max-w-6xl mx-auto bg-gray-900 border-4 border-red-700 rounded-3xl p-10 shadow-2xl animate-fadeInUp">
              <h1 className="text-5xl font-bold text-center mb-12 animate-fadeInUp">
                PAINEL DO <span className="text-red-600">BARBEIRO</span>
              </h1>
              <div className="w-56 h-1 bg-blue-600 mx-auto mb-12 animate-expandLine"></div>

              {agendamentos.length === 0 ? (
                <p className="text-center text-2xl text-gray-400 py-20 animate-fadeInUp">Agenda vazia por enquanto</p>
              ) : (
                <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-8">
                  {agendamentos.map((a, i) => (
                    <div key={i} className="bg-gray-800 border-4 border-red-700 rounded-2xl p-8 hover:-translate-y-4 hover:shadow-2xl transition duration-500 animate-slideUp shadow-xl" style={{ animationDelay: `${i * 0.2}s` }}>
                      <h3 className="text-2xl font-bold">{a.nome}</h3>
                      <p className="text-red-400 text-xl mt-2">{a.servico}</p>
                      <p className="text-gray-300 mt-4">
                        {new Date(a.data).toLocaleDateString("pt-BR")} às {a.hora}h
                      </p>
                      <p className="mt-4 text-lg font-bold">{a.status}</p>
                      <div className="grid grid-cols-2 gap-4 mt-6">
                        {a.status !== "Concluído" && (
                          <button onClick={() => concluir(i)} className="bg-white text-black font-bold py-3 rounded-xl hover:scale-105 transition duration-300">
                            Concluído
                          </button>
                        )}
                        <button onClick={() => cancelar(i)} className="bg-red-800 font-bold py-3 rounded-xl hover:scale-105 transition duration-300">
                          Excluir
                        </button>
                      </div>
                    </div>
                  ))}
                </div>
              )}

              <button onClick={logout} className="mt-12 w-full bg-gray-800 hover:bg-gray-700 py-4 rounded-xl font-bold text-lg animate-fadeInUp">
                Sair do Painel
              </button>
            </div>
            <Footer />
          </div>
        )}
      </div>
    </>
  );
}
