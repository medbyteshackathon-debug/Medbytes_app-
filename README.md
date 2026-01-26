# Medbytes_app-
- React code that allows mothers in crisis zone to submit their symptoms to first responders 

  const ComponentFunction = function () {
  const React = require('react');
  const { useState } = React;
  const { View, Text, ScrollView, TouchableOpacity } = require('react-native');

  // ===== Colors =====
  const C = {
    critical: '#FF6B6B', // red
    high: '#FFA94D',     // orange
    medium: '#FFD93D',   // yellow
    low: '#6BCB77',      // green
    bg: '#F4F4F9',
    btnPrimary: '#FFB6C1', 
    btnSecondary: '#A0AEC0', // grey
    optionSelected: '#FFAD33', // highlight for selected exposures
    text: '#000000'
  };

  const [tab, setTab] = useState('motherDashboard');
  const [mothers, setMothers] = useState([]);
  const [currentFormId, setCurrentFormId] = useState(null);
  const [stage, setStage] = useState('');
  const [ageGroup, setAgeGroup] = useState('');
  const [firstBaby, setFirstBaby] = useState(true);
  const [prevBabies, setPrevBabies] = useState('');
  const [miscarriage, setMiscarriage] = useState(false);
  const [miscarriagesCount, setMiscarriagesCount] = useState('');
  const [exposures, setExposures] = useState([]);
  const [formExpanded, setFormExpanded] = useState({});

  const toggle = (arr, v) => arr.includes(v) ? arr.filter(x => x !== v) : arr.concat([v]);
  const generateId = () => 'MOM-' + Math.random().toString(36).substring(2, 8).toUpperCase();

  const calculatePriority = () => {
    let score = 0;
    if(stage==='third') score+=3;
    else if(stage==='second') score+=2;
    else if(stage==='first') score+=1;
    score += exposures.length;
    if(!firstBaby) score += parseInt(prevBabies||0);
    if(miscarriage) score += 2+parseInt(miscarriagesCount||0);
    if(ageGroup === '<18' || ageGroup==='35+') score+=2;

    if(score>=9) return 'CRITICAL';
    if(score>=6) return 'HIGH';
    if(score>=3) return 'MEDIUM';
    return 'LOW';
  };

  const getColor = (priority) => {
    if(priority==='CRITICAL') return C.critical;
    if(priority==='HIGH') return C.high;
    if(priority==='MEDIUM') return C.medium;
    return C.low;
  };
  const getIcon = (priority) => (priority==='LOW'?'✓':'!');

  const submitForm = () => {
    const newId = currentFormId||generateId();
    const newPriority = calculatePriority();
    const newForm = { id:newId, priority:newPriority, stage, ageGroup, firstBaby, prevBabies, miscarriage, miscarriagesCount, exposures };
    const index = mothers.findIndex(m=>m.id===newId);
    let newMothers = [...mothers];
    if(index>=0) newMothers[index]=newForm;
    else newMothers.unshift(newForm);
    setMothers(newMothers);
    resetForm();
    setTab('motherDashboard');
  };

  const resetForm = () => {
    setStage(''); setAgeGroup(''); setFirstBaby(true); setPrevBabies('');
    setMiscarriage(false); setMiscarriagesCount(''); setExposures([]); setCurrentFormId(null);
  };

  const editForm = (form) => {
    setStage(form.stage); setAgeGroup(form.ageGroup); setFirstBaby(form.firstBaby);
    setPrevBabies(form.prevBabies); setMiscarriage(form.miscarriage); setMiscarriagesCount(form.miscarriagesCount);
    setExposures(form.exposures); setCurrentFormId(form.id); setTab('form');
  };

  const deleteForm = (formId) => setMothers(mothers.filter(m=>m.id!==formId));

  const s = {
    container:{flex:1, backgroundColor:C.bg, padding:16},
    title:{fontSize:22, fontWeight:'bold', color:C.text, marginBottom:12},
    sectionTitle:{fontSize:18, fontWeight:'bold', marginVertical:8, color:C.text},
    btn:{padding:14,borderRadius:14,marginVertical:6,justifyContent:'center',alignItems:'center'},
    btnPrimary:{backgroundColor:C.btnPrimary},
    btnSecondary:{backgroundColor:C.btnSecondary},
    btnText:{color:'#000', fontWeight:'bold', textAlign:'center'},
    option:{padding:12,borderRadius:12,marginVertical:4,backgroundColor:C.btnPrimary,justifyContent:'center',alignItems:'center'},
    optionSelected:{padding:12,borderRadius:12,marginVertical:4,backgroundColor:C.optionSelected,justifyContent:'center',alignItems:'center'}
  };

  const Tabs = React.createElement(
    View,{style:{flexDirection:'row',marginBottom:8}},
    ['motherDashboard','firstResponder'].map(t =>
      React.createElement(TouchableOpacity,{
        key:t,
        style:{...(tab===t?{...s.btn,...s.btnPrimary}:{...s.btn,...s.btnSecondary}), flex:1},
        onPress:()=>setTab(t)
      },React.createElement(Text,{style:s.btnText}, t==='motherDashboard'?'MOTHER':'FIRST RESPONDER'))
    )
  );

  // ===== Form Page =====
  const FormPage = React.createElement(
    ScrollView,{style:s.container},
    Tabs,
    React.createElement(Text,{style:s.title},'Mother Form'),

    React.createElement(Text,{style:s.sectionTitle},'Pregnancy Stage'),
    ['first','second','third'].map(v=>
      React.createElement(TouchableOpacity,{
        key:v, style:stage===v?s.optionSelected:s.option, onPress:()=>setStage(v)
      },React.createElement(Text,{style:s.btnText},v.toUpperCase()))
    ),

    React.createElement(Text,{style:s.sectionTitle},'Age Group'),
    ['<18','18-34','35+'].map(v=>
      React.createElement(TouchableOpacity,{
        key:v, style:ageGroup===v?s.optionSelected:s.option, onPress:()=>setAgeGroup(v)
      },React.createElement(Text,{style:s.btnText},v))
    ),

    React.createElement(Text,{style:s.sectionTitle},'First Baby?'),
    [true,false].map(v=>
      React.createElement(TouchableOpacity,{
        key:v.toString(), style:firstBaby===v?s.optionSelected:s.option, onPress:()=>setFirstBaby(v)
      },React.createElement(Text,{style:s.btnText},v?'Yes':'No'))
    ),

    React.createElement(Text,{style:s.sectionTitle},'History of Miscarriage?'),
    [true,false].map(v=>
      React.createElement(TouchableOpacity,{
        key:v.toString(), style:miscarriage===v?s.optionSelected:s.option, onPress:()=>setMiscarriage(v)
      },React.createElement(Text,{style:s.btnText},v?'Yes':'No'))
    ),

    React.createElement(Text,{style:s.sectionTitle},'Exposures / Things Affecting Pregnancy'),
    ['chemical','smoke','injury','bleeding'].map(v=>
      React.createElement(TouchableOpacity,{
        key:v, style: exposures.includes(v)?s.optionSelected:s.option, onPress:()=>setExposures(toggle(exposures,v))
      },React.createElement(Text,{style:s.btnText},v.toUpperCase()))
    ),

    // Submit & View Status Buttons (both grey)
    React.createElement(TouchableOpacity,{style:{...s.btn,...s.btnSecondary}, onPress:submitForm},
      React.createElement(Text,{style:s.btnText},'SUBMIT FORM')
    ),

    React.createElement(TouchableOpacity,{style:{...s.btn,...s.btnSecondary}, onPress:()=>setTab('status')},
      React.createElement(Text,{style:s.btnText},'VIEW STATUS')
    )
  );

  // ===== Mother Dashboard =====
  const MotherDashboard = React.createElement(
    ScrollView,{style:s.container},
    Tabs,
    React.createElement(Text,{style:s.title},'Your Forms'),
    React.createElement(TouchableOpacity,{style:{...s.btn,...s.btnPrimary},onPress:()=>{resetForm();setTab('form');}},
      React.createElement(Text,{style:s.btnText},'+ ADD / EDIT FORM')
    ),
    mothers.map(form=>
      React.createElement(View,{key:form.id,style:{padding:16,borderRadius:16,marginVertical:8,backgroundColor:getColor(form.priority)}},
        React.createElement(Text,{style:{fontWeight:'bold',color:'#000'}},form.id),
        React.createElement(Text,{style:{color:'#000'}},`${getIcon(form.priority)} ${form.priority}`),
        React.createElement(TouchableOpacity,{style:s.option,onPress:()=>editForm(form)},
          React.createElement(Text,{style:s.btnText},'EDIT')
        ),
        React.createElement(TouchableOpacity,{style:s.option,onPress:()=>{setCurrentFormId(form.id);setTab('status');}},
          React.createElement(Text,{style:s.btnText},'VIEW STATUS')
        )
      )
    )
  );

  // ===== Mother Status =====
  const StatusPage = React.createElement(
    ScrollView,{style:s.container},
    Tabs,
    React.createElement(Text,{style:s.title},'Your Status'),
    mothers.filter(m=>m.id===currentFormId).map(form=>
      React.createElement(View,{key:form.id,style:{padding:16,borderRadius:12,backgroundColor:getColor(form.priority),marginVertical:8}},
        React.createElement(Text,{style:{color:'#000',fontWeight:'bold'}},form.id),
        React.createElement(Text,{style:{color:'#000'}},`Priority: ${form.priority}`),
        React.createElement(Text,{style:{color:'#000'}},`Stage: ${form.stage.toUpperCase()}`),
        React.createElement(Text,{style:{color:'#000'}},`Age Group: ${form.ageGroup}`),
        React.createElement(Text,{style:{color:'#000'}},`First Baby: ${form.firstBaby?'Yes':'No'}`),
        React.createElement(Text,{style:{color:'#000'}},`Miscarriage: ${form.miscarriage?'Yes':'No'}`),
        React.createElement(Text,{style:{color:'#000'}},`Exposures: ${form.exposures.join(', ')}`)
      )
    ),
    React.createElement(TouchableOpacity,{style:{...s.btn,...s.btnSecondary},onPress:()=>setTab('motherDashboard')},
      React.createElement(Text,{style:s.btnText},'BACK')
    )
  );

  // ===== First Responder =====
  const FirstResponderPage = React.createElement(
    ScrollView,{style:s.container},
    Tabs,
    React.createElement(Text,{style:s.title},'Submitted Forms'),
    mothers.sort((a,b)=>{const o={CRITICAL:3,HIGH:2,MEDIUM:1,LOW:0}; return o[b.priority]-o[a.priority];})
      .map(form=>
        React.createElement(View,{key:form.id,style:{padding:16,borderRadius:12,marginVertical:6,backgroundColor:getColor(form.priority)}},
          React.createElement(TouchableOpacity,{onPress:()=>setFormExpanded({...formExpanded,[form.id]:!formExpanded[form.id]})},
            React.createElement(Text,{style:{fontWeight:'bold',color:'#000'}},`${form.id} (${getIcon(form.priority)} ${form.priority})`)
          ),
          formExpanded[form.id] && React.createElement(View,null,
            React.createElement(Text,{style:{color:'#000'}},`Stage: ${form.stage.toUpperCase()}`),
            React.createElement(Text,{style:{color:'#000'}},`Age Group: ${form.ageGroup}`),
            React.createElement(Text,{style:{color:'#000'}},`First Baby: ${form.firstBaby?'Yes':'No'}`),
            React.createElement(Text,{style:{color:'#000'}},`Miscarriage: ${form.miscarriage?'Yes':'No'}`),
            React.createElement(Text,{style:{color:'#000'}},`Exposures: ${form.exposures.join(', ')}`),
            React.createElement(TouchableOpacity,{style:{...s.btn,...s.btnPrimary},onPress:()=>deleteForm(form.id)},
              React.createElement(Text,{style:s.btnText},'COMPLETE / REMOVE')
            )
          )
        )
      )
  );

  if(tab==='motherDashboard') return MotherDashboard;
  if(tab==='form') return FormPage;
  if(tab==='status') return StatusPage;
  if(tab==='firstResponder') return FirstResponderPage;
  return null;
};

return ComponentFunction;
