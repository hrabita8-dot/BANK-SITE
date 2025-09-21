// Demo banking app (static, localStorage)
(function(){
  const demoUser = {email:'user@demo.com', password:'password', name:'Demo User'};
  const initialState = {
    balance: 2375.42,
    transactions: [
      {id:1, date: new Date().toISOString(), desc:'Deposit', amount:2375.42, type:'credit'}
    ]
  };

  // Utilities
  function $(id){return document.getElementById(id)}
  function formatCurrency(n){ return n.toLocaleString('en-US',{style:'currency',currency:'USD'}); }
  function nowDate(){ return new Date().toLocaleString(); }
  function toast(msg){ const t=$('toast'); t.textContent=msg; t.classList.remove('hidden'); setTimeout(()=>t.classList.add('hidden'),3000); }

  // Load or init state
  const STATE_KEY = 'hb_demo_state_v1';
  function loadState(){
    try{
      const raw = localStorage.getItem(STATE_KEY);
      if(!raw){ localStorage.setItem(STATE_KEY, JSON.stringify(initialState)); return JSON.parse(JSON.stringify(initialState)); }
      return JSON.parse(raw);
    }catch(e){ localStorage.setItem(STATE_KEY, JSON.stringify(initialState)); return JSON.parse(JSON.stringify(initialState)); }
  }
  function saveState(st){ localStorage.setItem(STATE_KEY, JSON.stringify(st)); }

  let state = loadState();

  // Auth
  function showDashboard(){ $('login-screen').classList.add('hidden'); $('dashboard').classList.remove('hidden'); $('btn-logout').style.display='inline-block'; render(); }
  function showLogin(){ $('login-screen').classList.remove('hidden'); $('dashboard').classList.add('hidden'); $('btn-logout').style.display='none'; }

  // Render functions
  function render(){
    $('balance').textContent = formatCurrency(state.balance);
    $('balance-date').textContent = nowDate();
    const list = $('tx-list'); list.innerHTML='';
    const txs = state.transactions.slice().reverse();
    txs.forEach(tx=>{
      const el = document.createElement('div'); el.className='tx';
      el.innerHTML = '<div class="meta"><strong>'+tx.desc+'</strong><span class="muted small">'+new Date(tx.date).toLocaleString()+'</span></div><div class="amt">'+(tx.type==='debit' ? '-' : '')+formatCurrency(Math.abs(tx.amount))+'</div>';
      list.appendChild(el);
    });
  }

  // Event handlers
  $('login-form').addEventListener('submit', function(e){
    e.preventDefault();
    const email = $('login-email').value.trim();
    const pw = $('login-password').value;
    if(email===demoUser.email && pw===demoUser.password){
      showDashboard();
      toast('Signed in — welcome back!');
    } else {
      toast('Invalid demo credentials. Use user@demo.com / password');
    }
  });

  $('btn-logout').addEventListener('click', function(){ showLogin(); toast('Logged out'); });

  $('transfer-form').addEventListener('submit', function(e){
    e.preventDefault();
    const recipient = $('transfer-recipient').value.trim();
    const amount = parseFloat($('transfer-amount').value);
    const note = $('transfer-note').value.trim();
    if(!recipient || !amount || amount<=0){ toast('Please enter a valid recipient and amount'); return; }
    if(amount > state.balance){ toast('Insufficient funds'); return; }

    // Create transaction
    const tx = { id: Date.now(), date: new Date().toISOString(), desc: 'Sent to ' + recipient + (note ? ' — '+note : ''), amount: -Math.abs(amount), type:'debit' };
    state.transactions.push(tx);
    state.balance = +(state.balance - amount).toFixed(2);
    saveState(state);
    render();
    toast('Sent ' + formatCurrency(amount) + ' to ' + recipient);
    // Clear form
    $('transfer-recipient').value=''; $('transfer-amount').value=''; $('transfer-note').value='';
  });

  // On load: show login
  showLogin();

  // Expose reset helper
  window.HB_DEMO = {state, reset:()=>{ localStorage.removeItem(STATE_KEY); state = loadState(); render(); toast('Reset demo data'); }};
})();