
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Orden de Trabajo Topografía</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @media print {
            .no-print {
                display: none !important;
            }
            body {
                print-color-adjust: exact;
                -webkit-print-color-adjust: exact;
            }
        }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState } = React;

        function TopoWorkOrder() {
            const [workOrder, setWorkOrder] = useState({
                codigo: '033P01F009',
                version: '1',
                departamento: '',
                distribucion: 'CENTRO DE CONTROL MAESTRO DE AC Y ALC (CCM)',
                actividad: 'GEOREFERENCIAR Y REPLANTEAR REDES DE ACUEDUCTO',
                integrantes: 'VLADIMIR GARCES (PS), DARWIIN CANDEDE (PS)',
                conduce: 'JULIAN ANDRES MONTOYA PLAZA (20333)',
                fecha: { año: 2025, mes: '09', dia: '24' },
                horaSalida: '10:05',
                horaEntrada: '18:35',
                ordenNo: '',
                placasVehiculo: 'ONK 254'
            });

            const [footer, setFooter] = useState({
                observaciones: 'PRESENTACIÓN PREVENCIÓN CONTRA EL CÁNCER DE PIEL Y PRÓSTATA 8:30 - 9:20',
                programa: 'JULIAN ANDRES MONTOYA PLAZA',
                nombre1: 'JULIAN ANDRES MONTOYA PLAZA',
                registro: '20333',
                ejecuta: 'JULIAN ANDRES MONTOYA PLAZA',
                nombre2: 'JULIAN ANDRES MONTOYA PLAZA',
                nota1: 'HACER USO DE ELEMENTOS DE SEGURIDAD INDUSTRIAL.',
                nota2: 'LOS ELEMENTOS INSERTOS EN EL ACUEDUCTO SE DEBEN REFERENCIAR DESCUBIERTOS.'
            });

            const [showEmailModal, setShowEmailModal] = useState(false);
            const [emailList, setEmailList] = useState(['']);
            const [emailSubject, setEmailSubject] = useState('Orden de Trabajo Topografía');
            const [emailMessage, setEmailMessage] = useState('Adjunto encontrará la orden de trabajo de topografía.');

            const [activities, setActivities] = useState([
                {
                    id: 1,
                    actividad: 'REFERENCIAR VALVULA REPUESTA D.20 EN CONTRATO CO-300-3132-2024',
                    direccionProg: 'CR 14 CL 6 A',
                    proyectoBarrio: 'SAN JUAN BOSCO',
                    comuna: '9',
                    municipio: 'CALI',
                    tipoProyecto: 'REP',
                    horaInicio: '14:10',
                    horaTermina: '14:50',
                    refEnsamblador: 'REF',
                    direccionEjec: 'CR 14 CL 6 A',
                    plano: 'N/A',
                    cartera: 'N/A',
                    trayecto: 'N/A',
                    instalada: 'SI',
                    fechaProxima: 'N/A',
                    estadoActividad: 'T',
                    estadoObra: 'E',
                    observaciones: 'SE REFERENCIÓ VALVULA REPUESTA D.20" EN TUBERIA CCP'
                }
            ]);

            const addActivity = () => {
                const newActivity = {
                    id: activities.length + 1,
                    actividad: '',
                    direccionProg: '',
                    proyectoBarrio: '',
                    comuna: '',
                    municipio: 'CALI',
                    tipoProyecto: 'REP',
                    horaInicio: '',
                    horaTermina: '',
                    refEnsamblador: '',
                    direccionEjec: '',
                    plano: 'N/A',
                    cartera: 'N/A',
                    trayecto: 'N/A',
                    instalada: '',
                    fechaProxima: '',
                    estadoActividad: '',
                    estadoObra: '',
                    observaciones: ''
                };
                setActivities([...activities, newActivity]);
            };

            const updateActivity = (id, field, value) => {
                setActivities(activities.map(act => 
                    act.id === id ? { ...act, [field]: value } : act
                ));
            };

            const deleteActivity = (id) => {
                setActivities(activities.filter(act => act.id !== id));
            };

            const updateWorkOrder = (field, value) => {
                setWorkOrder({ ...workOrder, [field]: value });
            };

            const updateFecha = (field, value) => {
                setWorkOrder({ 
                    ...workOrder, 
                    fecha: { ...workOrder.fecha, [field]: value }
                });
            };

            const updateFooter = (field, value) => {
                setFooter({ ...footer, [field]: value });
            };

            const addEmailField = () => {
                setEmailList([...emailList, '']);
            };

            const updateEmail = (index, value) => {
                const newList = [...emailList];
                newList[index] = value;
                setEmailList(newList);
            };

            const removeEmail = (index) => {
                const newList = emailList.filter((_, i) => i !== index);
                setEmailList(newList.length > 0 ? newList : ['']);
            };

            const handleSendEmail = () => {
                const validEmails = emailList.filter(email => email.trim() !== '');
                
                if (validEmails.length === 0) {
                    alert('Por favor, ingrese al menos un correo electrónico válido.');
                    return;
                }

                const emailBody = encodeURIComponent(emailMessage);
                const subject = encodeURIComponent(emailSubject);
                const recipients = validEmails.join(',');
                
                window.location.href = `mailto:${recipients}?subject=${subject}&body=${emailBody}`;
                
                alert(`Preparando correo para enviar a: ${validEmails.join(', ')}`);
                setShowEmailModal(false);
            };

            const saveWorkOrder = () => {
                const data = {
                    workOrder,
                    activities,
                    footer
                };
                
                const jsonData = JSON.stringify(data, null, 2);
                const blob = new Blob([jsonData], { type: 'application/json' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `Orden_Trabajo_${workOrder.codigo}_${workOrder.fecha.año}${workOrder.fecha.mes}${workOrder.fecha.dia}.json`;
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
                URL.revokeObjectURL(url);
                
                alert('Orden de trabajo guardada exitosamente!');
            };

            const exportToExcel = () => {
                const wb = XLSX.utils.book_new();
                
                const headerData = [
                    ['ORDEN DE TRABAJO TOPOGRAFÍA'],
                    [],
                    ['CÓDIGO', workOrder.codigo, '', 'VERSIÓN', workOrder.version],
                    [],
                    ['DEPARTAMENTO', workOrder.departamento],
                    ['ÁREA FUNCIONAL'],
                    ['DISTRIBUCIÓN', workOrder.distribucion],
                    ['ACTIVIDAD', workOrder.actividad],
                    ['INTEGRANTES EMCALI', workOrder.integrantes],
                    ['CONDUCE', workOrder.conduce],
                    [],
                    ['FECHA', `${workOrder.fecha.año}/${workOrder.fecha.mes}/${workOrder.fecha.dia}`],
                    ['HORA SALIDA', workOrder.horaSalida],
                    ['HORA ENTRADA', workOrder.horaEntrada],
                    ['ORDEN No.', workOrder.ordenNo],
                    ['PLACAS VEHÍCULO', workOrder.placasVehiculo],
                    []
                ];

                const activityHeaders = [
                    'Actividad a Realizar',
                    'Dirección Programación',
                    'Proyecto/Barrio',
                    'Comuna',
                    'Municipio',
                    'Tipo Proyecto',
                    'Hora Inicio',
                    'Hora Termina',
                    'Ref. Ensamblador',
                    'Dirección Ejecución',
                    'Plano No.',
                    'Cartera/Trayecto No.',
                    'Pág.',
                    'Instalada/Empleada',
                    'Fecha Próxima',
                    'Estado Actividad',
                    'Estado Obra',
                    'Observaciones'
                ];

                const activityData = activities.map(act => [
                    act.actividad,
                    act.direccionProg,
                    act.proyectoBarrio,
                    act.comuna,
                    act.municipio,
                    act.tipoProyecto,
                    act.horaInicio,
                    act.horaTermina,
                    act.refEnsamblador,
                    act.direccionEjec,
                    act.plano,
                    act.cartera,
                    act.trayecto,
                    act.instalada,
                    act.fechaProxima,
                    act.estadoActividad,
                    act.estadoObra,
                    act.observaciones
                ]);

                const allData = [
                    ...headerData,
                    activityHeaders,
                    ...activityData,
                    [],
                    ['OBSERVACIONES', footer.observaciones],
                    [],
                    ['PROGRAMA', footer.programa],
                    ['NOMBRE', footer.nombre1],
                    ['REGISTRO', footer.registro],
                    ['EJECUTA', footer.ejecuta],
                    ['NOMBRE', footer.nombre2],
                    [],
                    ['NOTA 1', footer.nota1],
                    ['NOTA 2', footer.nota2]
                ];

                const ws = XLSX.utils.aoa_to_sheet(allData);

                const colWidths = [
                    { wch: 30 }, { wch: 20 }, { wch: 20 }, { wch: 8 },
                    { wch: 12 }, { wch: 12 }, { wch: 10 }, { wch: 10 },
                    { wch: 12 }, { wch: 20 }, { wch: 10 }, { wch: 10 },
                    { wch: 8 }, { wch: 15 }, { wch: 12 }, { wch: 12 },
                    { wch: 12 }, { wch: 30 }
                ];
                ws['!cols'] = colWidths;

                XLSX.utils.book_append_sheet(wb, ws, 'Orden de Trabajo');

                const fileName = `Orden_Trabajo_${workOrder.codigo}_${workOrder.fecha.año}${workOrder.fecha.mes}${workOrder.fecha.dia}.xlsx`;
                XLSX.writeFile(wb, fileName);
            };

            return (
                <div className="w-full min-h-screen bg-white p-4">
                    <div className="max-w-[1400px] mx-auto">
                        {/* Header */}
                        <div className="border-2 border-black mb-4">
                            <div className="bg-gray-200 text-center py-2 border-b-2 border-black">
                                <h1 className="text-lg font-bold">ORDEN DE TRABAJO TOPOGRAFÍA</h1>
                            </div>
                            
                            <div className="grid grid-cols-2 border-b-2 border-black">
                                <div className="p-2 border-r-2 border-black">
                                    <span className="font-bold">CÓDIGO: </span>
                                    <input 
                                        type="text" 
                                        value={workOrder.codigo}
                                        onChange={(e) => updateWorkOrder('codigo', e.target.value)}
                                        className="border border-gray-300 px-2 py-1 ml-2"
                                    />
                                </div>
                                <div className="p-2">
                                    <span className="font-bold">VERSIÓN: </span>
                                    <input 
                                        type="text" 
                                        value={workOrder.version}
                                        onChange={(e) => updateWorkOrder('version', e.target.value)}
                                        className="border border-gray-300 px-2 py-1 ml-2 w-20"
                                    />
                                </div>
                            </div>

                            <div className="grid grid-cols-3 text-sm">
                                <div className="col-span-2 border-r-2 border-black">
                                    <div className="p-2 border-b border-black">
                                        <span className="font-bold">DEPARTAMENTO:</span>
                                        <input 
                                            type="text" 
                                            value={workOrder.departamento}
                                            onChange={(e) => updateWorkOrder('departamento', e.target.value)}
                                            className="border border-gray-300 px-2 py-1 ml-2 w-full mt-1"
                                        />
                                    </div>
                                    <div className="p-2 border-b border-black">
                                        <span className="font-bold">DISTRIBUCIÓN:</span>
                                        <input 
                                            type="text" 
                                            value={workOrder.distribucion}
                                            onChange={(e) => updateWorkOrder('distribucion', e.target.value)}
                                            className="border border-gray-300 px-2 py-1 ml-2 w-full mt-1"
                                        />
                                    </div>
                                    <div className="p-2 border-b border-black">
                                        <span className="font-bold">ACTIVIDAD:</span>
                                        <input 
                                            type="text" 
                                            value={workOrder.actividad}
                                            onChange={(e) => updateWorkOrder('actividad', e.target.value)}
                                            className="border border-gray-300 px-2 py-1 ml-2 w-full mt-1"
                                        />
                                    </div>
                                    <div className="p-2 border-b border-black">
                                        <span className="font-bold">INTEGRANTES EMCALI (Nombre y Registro):</span>
                                        <textarea 
                                            value={workOrder.integrantes}
                                            onChange={(e) => updateWorkOrder('integrantes', e.target.value)}
                                            className="border border-gray-300 px-2 py-1 ml-2 w-full mt-1"
                                            rows="2"
                                        />
                                    </div>
                                    <div className="p-2">
                                        <span className="font-bold">CONDUCE:</span>
                                        <input 
                                            type="text" 
                                            value={workOrder.conduce}
                                            onChange={(e) => updateWorkOrder('conduce', e.target.value)}
                                            className="border border-gray-300 px-2 py-1 ml-2 w-full mt-1"
                                        />
                                    </div>
                                </div>
                                
                                <div>
                                    <div className="p-2 border-b border-black">
                                        <div className="font-bold mb-2">FECHA</div>
                                        <div className="grid grid-cols-3 gap-2">
                                            <div>
                                                <label className="text-xs">AÑO</label>
                                                <input 
                                                    type="text" 
                                                    value={workOrder.fecha.año}
                                                    onChange={(e) => updateFecha('año', e.target.value)}
                                                    className="border border-gray-300 px-1 py-1 w-full"
                                                />
                                            </div>
                                            <div>
                                                <label className="text-xs">MES</label>
                                                <input 
                                                    type="text" 
                                                    value={workOrder.fecha.mes}
                                                    onChange={(e) => updateFecha('mes', e.target.value)}
                                                    className="border border-gray-300 px-1 py-1 w-full"
                                                />
                                            </div>
                                            <div>
                                                <label className="text-xs">DÍA</label>
                                                <input 
                                                    type="text" 
                                                    value={workOrder.fecha.dia}
                                                    onChange={(e) => updateFecha('dia', e.target.value)}
                                                    className="border border-gray-300 px-1 py-1 w-full"
                                                />
                                            </div>
                                        </div>
                                    </div>
                                    <div className="p-2 border-b border-black">
                                        <label className="text-xs font-bold">HORA SALIDA:</label>
                                        <input 
                                            type="text" 
                                            value={workOrder.horaSalida}
                                            onChange={(e) => updateWorkOrder('horaSalida', e.target.value)}
                                            className="border border-gray-300 px-2 py-1 w-full mt-1"
                                        />
                                    </div>
                                    <div className="p-2 border-b border-black">
                                        <label className="text-xs font-bold">HORA ENTRADA:</label>
                                        <input 
                                            type="text" 
                                            value={workOrder.horaEntrada}
                                            onChange={(e) => updateWorkOrder('horaEntrada', e.target.value)}
                                            className="border border-gray-300 px-2 py-1 w-full mt-1"
                                        />
                                    </div>
                                    <div className="p-2 border-b border-black">
                                        <label className="text-xs font-bold">ORDEN No.:</label>
                                        <input 
                                            type="text" 
                                            value={workOrder.ordenNo}
                                            onChange={(e) => updateWorkOrder('ordenNo', e.target.value)}
                                            className="border border-gray-300 px-2 py-1 w-full mt-1"
                                        />
                                    </div>
                                    <div className="p-2">
                                        <label className="text-xs font-bold">PLACAS VEHÍCULO:</label>
                                        <input 
                                            type="text" 
                                            value={workOrder.placasVehiculo}
                                            onChange={(e) => updateWorkOrder('placasVehiculo', e.target.value)}
                                            className="border border-gray-300 px-2 py-1 w-full mt-1"
                                        />
                                    </div>
                                </div>
                            </div>
                        </div>

                        {/* Activities Table */}
                        <div className="border-2 border-black overflow-x-auto">
                            <table className="w-full text-xs border-collapse">
                                <thead className="bg-gray-200">
                                    <tr>
                                        <th className="border border-black p-1 min-w-[150px]">Actividad a Realizar</th>
                                        <th className="border border-black p-1 min-w-[100px]">Dirección Programación</th>
                                        <th className="border border-black p-1 min-w-[100px]">Proyecto/ Barrio</th>
                                        <th className="border border-black p-1 w-12">Comuna</th>
                                        <th className="border border-black p-1 w-16">Municipio</th>
                                        <th className="border border-black p-1 w-16">Tipo Proyecto</th>
                                        <th className="border border-black p-1 w-16">Hora Inicio</th>
                                        <th className="border border-black p-1 w-16">Hora Termina</th>
                                        <th className="border border-black p-1 w-16">Ref. Ensamblador</th>
                                        <th className="border border-black p-1 min-w-[100px]">Dirección Ejecución</th>
                                        <th className="border border-black p-1 w-12">Plano No.</th>
                                        <th className="border border-black p-1 w-12">Cartera Trayecto No.</th>
                                        <th className="border border-black p-1 w-12">Pág.</th>
                                        <th className="border border-black p-1 w-16">Instalada Empleada o Sin Prueba</th>
                                        <th className="border border-black p-1 w-20">Fecha Próxima Actividad</th>
                                        <th className="border border-black p-1 w-16">Estado Actividad</th>
                                        <th className="border border-black p-1 w-16">Estado Obra</th>
                                        <th className="border border-black p-1 min-w-[150px]">Observaciones</th>
                                        <th className="border border-black p-1 w-12 no-print">Acciones</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    {activities.map((activity) => (
                                        <tr key={activity.id} className="hover:bg-gray-50">
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.actividad}
                                                    onChange={(e) => updateActivity(activity.id, 'actividad', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.direccionProg}
                                                    onChange={(e) => updateActivity(activity.id, 'direccionProg', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.proyectoBarrio}
                                                    onChange={(e) => updateActivity(activity.id, 'proyectoBarrio', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.comuna}
                                                    onChange={(e) => updateActivity(activity.id, 'comuna', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.municipio}
                                                    onChange={(e) => updateActivity(activity.id, 'municipio', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.tipoProyecto}
                                                    onChange={(e) => updateActivity(activity.id, 'tipoProyecto', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.horaInicio}
                                                    onChange={(e) => updateActivity(activity.id, 'horaInicio', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.horaTermina}
                                                    onChange={(e) => updateActivity(activity.id, 'horaTermina', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.refEnsamblador}
                                                    onChange={(e) => updateActivity(activity.id, 'refEnsamblador', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.direccionEjec}
                                                    onChange={(e) => updateActivity(activity.id, 'direccionEjec', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.plano}
                                                    onChange={(e) => updateActivity(activity.id, 'plano', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.cartera}
                                                    onChange={(e) => updateActivity(activity.id, 'cartera', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.trayecto}
                                                    onChange={(e) => updateActivity(activity.id, 'trayecto', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.instalada}
                                                    onChange={(e) => updateActivity(activity.id, 'instalada', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.fechaProxima}
                                                    onChange={(e) => updateActivity(activity.id, 'fechaProxima', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.estadoActividad}
                                                    onChange={(e) => updateActivity(activity.id, 'estadoActividad', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <input 
                                                    type="text" 
                                                    value={activity.estadoObra}
                                                    onChange={(e) => updateActivity(activity.id, 'estadoObra', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                />
                                            </td>
                                            <td className="border border-black p-1">
                                                <textarea 
                                                    value={activity.observaciones}
                                                    onChange={(e) => updateActivity(activity.id, 'observaciones', e.target.value)}
                                                    className="w-full px-1 py-1 text-xs"
                                                    rows="2"
                                                />
                                            </td>
                                            <td className="border border-black p-1 no-print">
                                                <button 
                                                    onClick={() => deleteActivity(activity.id)}
                                                    className="bg-red-500 hover:bg-red-600 text-white p-1 rounded"
                                                >
                                                    ✕
                                                </button>
                                            </td>
                                        </tr>
                                    ))}
                                </tbody>
                            </table>
                        </div>

                        {/* Footer Section - Editable */}
                        <div className="border-2 border-black mt-4 p-3 bg-gray-50">
                            <div className="mb-3">
                                <label className="font-bold text-sm block mb-1">OBSERVACIONES:</label>
                                <textarea 
                                    value={footer.observaciones}
                                    onChange={(e) => updateFooter('observaciones', e.target.value)}
                                    className="w-full border border-gray-300 px-3 py-2 text-sm rounded"
                                    rows="2"
                                />
                            </div>
                            
                            <div className="grid grid-cols-2 gap-4 mb-3">
                                <div>
                                    <div className="mb-2">
                                        <label className="font-bold text-xs block">PROGRAMA:</label>
                                        <input 
                                            type="text" 
                                            value={footer.programa}
                                            onChange={(e) => updateFooter('programa', e.target.value)}
                                            className="w-full border border-gray-300 px-2 py-1 text-sm rounded"
                                        />
                                    </div>
                                    <div className="mb-2">
                                        <label className="font-bold text-xs block">NOMBRE:</label>
                                        <input 
                                            type="text" 
                                            value={footer.nombre1}
                                            onChange={(e) => updateFooter('nombre1', e.target.value)}
                                            className="w-full border border-gray-300 px-2 py-1 text-sm rounded"
                                        />
                                    </div>
                                    <div className="mb-2">
                                        <label className="font-bold text-xs block">REGISTRO:</label>
                                        <input 
                                            type="text" 
                                            value={footer.registro}
                                            onChange={(e) => updateFooter('registro', e.target.value)}
                                            className="w-full border border-gray-300 px-2 py-1 text-sm rounded"
                                        />
                                    </div>
                                </div>
                                
                                <div>
                                    <div className="mb-2">
                                        <label className="font-bold text-xs block">EJECUTA:</label>
                                        <input 
                                            type="text" 
                                            value={footer.ejecuta}
                                            onChange={(e) => updateFooter('ejecuta', e.target.value)}
                                            className="w-full border border-gray-300 px-2 py-1 text-sm rounded"
                                        />
                                    </div>
                                    <div className="mb-2">
                                        <label className="font-bold text-xs block">NOMBRE:</label>
                                        <input 
                                            type="text" 
                                            value={footer.nombre2}
                                            onChange={(e) => updateFooter('nombre2', e.target.value)}
                                            className="w-full border border-gray-300 px-2 py-1 text-sm rounded"
                                        />
                                    </div>
                                </div>
                            </div>

                            <div className="border-t pt-3">
                                <div className="mb-2">
                                    <label className="font-bold text-xs block">NOTA 1:</label>
                                    <input 
                                        type="text" 
                                        value={footer.nota1}
                                        onChange={(e) => updateFooter('nota1', e.target.value)}
                                        className="w-full border border-gray-300 px-2 py-1 text-sm rounded"
                                    />
                                </div>
                                <div>
                                    <label className="font-bold text-xs block">NOTA 2:</label>
                                    <input 
                                        type="text" 
                                        value={footer.nota2}
                                        onChange={(e) => updateFooter('nota2', e.target.value)}
                                        className="w-full border border-gray-300 px-2 py-1 text-sm rounded"
                                    />
                                </div>
                            </div>
                        </div>

                        {/* Action Buttons */}
                        <div className="mt-4 flex gap-2 justify-end flex-wrap no-print">
                            <button 
                                onClick={addActivity}
                                className="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded flex items-center gap-2"
                            >
                                <span>➕</span>
                                Agregar Actividad
                            </button>
                            <button 
                                className="bg-green-500 hover:bg-green-600 text-white px-4 py-2 rounded flex items-center gap-2"
                                onClick={saveWorkOrder}
                            >
                                <span>💾</span>
                                Guardar
                            </button>
                            <button 
                                className="bg-orange-500 hover:bg-orange-600 text-white px-4 py-2 rounded flex items-center gap-2"
                                onClick={exportToExcel}
                            >
                                <span>📊</span>
                                Exportar Excel
                            </button>
                            <button 
                                className="bg-purple-500 hover:bg-purple-600 text-white px-4 py-2 rounded flex items-center gap-2"
                                onClick={() => window.print()}
                            >
                                <span>📄</span>
                                Exportar PDF
                            </button>
                            <button 
                                className="bg-cyan-500 hover:bg-cyan-600 text-white px-4 py-2 rounded flex items-center gap-2"
                                onClick={() => setShowEmailModal(true)}
                            >
                                <span>✉️</span>
                                Enviar por Email
                            </button>
                        </div>

                        {/* Email Modal */}
                        {showEmailModal && (
                            <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4">
                                <div className="bg-white rounded-lg shadow-xl max-w-2xl w-full max-h-[90vh] overflow-y-auto">
                                    <div className="p-6">
                                        <div className="flex justify-between items-center mb-4">
                                            <h2 className="text-xl font-bold">Enviar Orden de Trabajo por Email</h2>
                                            <button 
                                                onClick={() => setShowEmailModal(false)}
                                                className="text-gray-500 hover:text-gray-700 text-2xl"
                                            >
                                                ✕
                                            </button>
                                        </div>

                                        <div className="mb-4">
                                            <label className="block font-bold mb-2">Asunto:</label>
                                            <input 
                                                type="text"
                                                value={emailSubject}
                                                onChange={(e) => setEmailSubject(e.target.value)}
                                                className="w-full border border-gray-300 rounded px-3 py-2"
                                                placeholder="Asunto del correo"
                                            />
                                        </div>

                                        <div className="mb-4">
                                            <label className="block font-bold mb-2">Mensaje:</label>
                                            <textarea 
                                                value={emailMessage}
                                                onChange={(e) => setEmailMessage(e.target.value)}
                                                className="w-full border border-gray-300 rounded px-3 py-2"
                                                rows="4"
                                                placeholder="Mensaje del correo"
                                            />
                                        </div>

                                        <div className="mb-4">
                                            <label className="block font-bold mb-2">Destinatarios:</label>
                                            {emailList.map((email, index) => (
                                                <div key={index} className="flex gap-2 mb-2">
                                                    <input 
                                                        type="email"
                                                        value={email}
                                                        onChange={(e) => updateEmail(index, e.target.value)}
                                                        className="flex-1 border border-gray-300 rounded px-3 py-2"
                                                        placeholder="correo@ejemplo.com"
                                                    />
                                                    {emailList.length > 1 && (
                                                        <button 
                                                            onClick={() => removeEmail(index)}
                                                            className="bg-red-500 hover:bg-red-600 text-white px-3 py-2 rounded"
                                                        >
                                                            ✕
                                                        </button>
                                                    )}
                                                </div>
                                            ))}
                                            <button 
                                                onClick={addEmailField}
                                                className="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded flex items-center gap-2 mt-2"
                                            >
                                                <span>➕</span>
                                                Agregar Destinatario
                                            </button>
                                        </div>

                                        <div className="flex gap-2 justify-end">
                                            <button 
                                                onClick={() => setShowEmailModal(false)}
                                                className="bg-gray-500 hover:bg-gray-600 text-white px-4 py-2 rounded"
                                            >
                                                Cancelar
                                            </button>
                                            <button 
                                                onClick={handleSendEmail}
                                                className="bg-cyan-500 hover:bg-cyan-600 text-white px-4 py-2 rounded flex items-center gap-2"
                                            >
                                                <span>✉️</span>
                                                Enviar
                                            </button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        )}
                    </div>
                </div>
            );
        }

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<TopoWorkOrder />);
    </script>
</body>
</html>
