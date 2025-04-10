//dashboard.jsx
import React, { useState, useEffect } from 'react';
import { Link, useNavigate, useLocation } from 'react-router-dom';
import Modal from 'react-modal';
import './Dashboard.css';
import './Modal.css';
import closeIcon from '/images/close.png';
import PedidosDashboard from './PedidosDashboard';

Modal.setAppElement('#root');

function Dashboard() {
    const [expanded, setExpanded] = useState({
        panel: true,
        clientes: false,
        motorizados: false,
        asesores: false,
        reportes: false,
    });

    const [arrowImages, setArrowImages] = useState({
        panel: '/images/down arrow.png',
        clientes: '/images/shadow arrow.png',
        motorizados: '/images/shadow arrow.png',
        asesores: '/images/shadow arrow.png',
        reportes: '/images/shadow arrow.png',
    });

    const [gearImages, setGearImages] = useState({
        panel: '/images/gear.png',
        clientes: '/images/shadow folder.png',
        motorizados: '/images/shadow file.png',
        asesores: '/images/shadow tv.png',
        reportes: '/images/shadow report.png',
    });

    const [spanColors, setSpanColors] = useState({
        panel: 'white',
        clientes: '#555d8b',
        motorizados: '#555d8b',
        asesores: '#555d8b',
        reportes: '#555d8b',
    });

    const [activeSection, setActiveSection] = useState('');

    const [searchTerm, setSearchTerm] = useState('');
    const [filterOptions, setFilterOptions] = useState({
        orden: '',
        delivery: '',
        tranzabilidad: '',
        importes: '',
        pagos: '',
        productos: '',
        fechaInicio: '',
        fechaFin: '',
    });

    const [modalIsOpen, setModalIsOpen] = useState(false);
    const [processingModalIsOpen, setProcessingModalIsOpen] = useState(false);
    const [completedModalIsOpen, setCompletedModalIsOpen] = useState(false);

    const [clients, setClients] = useState(() => {
        const storedClients = localStorage.getItem('clientsData');
        return storedClients ? JSON.parse(storedClients) : [];
    });
    const [newClient, setNewClient] = useState({
        nombre: '',
        asesor: '',
        estado: '',
        correo: '',
        celular: '',
        direccion: '',
        producto: '',
        diaIngreso: '',
        diaAtencion: '',
        diaProgramado: '',
        distrito: '',
        referencia: '',
        rangoHora: '',
        notas: '',
    });
    const [editIndex, setEditIndex] = useState(-1);

    const navigate = useNavigate();
    const location = useLocation();
    
    useEffect(() => {
        const path = location.pathname.split('/');
        const lastSegment = path[path.length - 1];
        
        if (lastSegment !== 'dashboard') {
            setActiveSection(lastSegment);
        }
    }, [location.pathname]);

    const openModal = () => {
        setModalIsOpen(true);
        document.body.classList.add('modal-open');
        setEditIndex(-1);
        setNewClient({
            nombre: '',
            asesor: '',
            estado: '',
            correo: '',
            celular: '',
            direccion: '',
            producto: '',
            diaIngreso: '',
            diaAtencion: '',
            diaProgramado: '',
            distrito: '',
            referencia: '',
            rangoHora: '',
            notas: '',
        });
    };

    const closeModal = () => {
        setModalIsOpen(false);
        document.body.classList.remove('modal-open');
        setProcessingModalIsOpen(false);
        setCompletedModalIsOpen(false);
    };

    const handleInputChange = (e) => {
        const { name, value } = e.target;
        setNewClient({ ...newClient, [name]: value });
    };

    const handleRegister = (e) => {
        e.preventDefault();
        setModalIsOpen(false);
        setProcessingModalIsOpen(true);

        setTimeout(() => {
            setProcessingModalIsOpen(false);
            setCompletedModalIsOpen(true);

            setTimeout(() => {
                setCompletedModalIsOpen(false);
                let updatedClients;
                if (editIndex === -1) {
                    updatedClients = [...clients, newClient];
                } else {
                    updatedClients = [...clients];
                    updatedClients[editIndex] = newClient;
                }
                setClients(updatedClients);
                localStorage.setItem('clientsData', JSON.stringify(updatedClients));
                console.log('Registro completado y proceso continuado.');
            }, 1000);
        }, 1000);
    };

    const toggleSection = (section) => {
        setExpanded({
            ...expanded,
            [section]: !expanded[section],
        });

        setArrowImages((prevArrowImages) => ({
            ...prevArrowImages,
            [section]: !expanded[section]
                ? '/images/down arrow.png'
                : '/images/shadow arrow.png',
        }));

        if (section === 'panel') {
            setGearImages((prevGearImages) => ({
                ...prevGearImages,
                panel: !expanded.panel ? '/images/gear.png' : '/images/shadow gear.png',
            }));
        } else {
            setGearImages((prevGearImages) => {
                let newGearImages = { ...prevGearImages };
                switch (section) {
                    case 'clientes':
                        newGearImages.clientes = !expanded.clientes ? '/images/folder.png' : '/images/shadow folder.png';
                        break;
                    case 'motorizados':
                        newGearImages.motorizados = !expanded.motorizados ? '/images/file.png' : '/images/shadow file.png';
                        break;
                    case 'asesores':
                        newGearImages.asesores = !expanded.asesores ? '/images/tv.png' : '/images/shadow tv.png';
                        break;
                    case 'reportes':
                        newGearImages.reportes = !expanded.reportes ? '/images/report.png' : '/images/shadow report.png';
                        break;
                    default:
                        break;
                }
                return newGearImages;
            });
        }

        setSpanColors((prevSpanColors) => ({
            ...prevSpanColors,
            [section]: !expanded[section] ? 'white' : '#555d8b',
        }));
    };

    const handleSectionClick = (section) => {
        setActiveSection(section);
        navigate(`/dashboard/${section}`);
    };

    const handleEdit = (index) => {
        setEditIndex(index);
        setNewClient(clients[index]);
        setModalIsOpen(true);
    };

    const handleDelete = (index) => {
        const updatedClients = clients.filter((_, i) => i !== index);
        setClients(updatedClients);
        localStorage.setItem('clientsData', JSON.stringify(updatedClients));
    };

    const [sidebarCollapsed, setSidebarCollapsed] = useState(false);

    const toggleSidebar = () => {
        setSidebarCollapsed(!sidebarCollapsed);
    };

    const openCloseIcon = (
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="lucide lucide-panel-left-close h-5 w-5"><rect width="18" height="18" x="3" y="3" rx="2"></rect><path d="M9 3v18"></path><path d="m16 15-3-3 3-3"></path></svg>
    );

    const openIcon = (
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="lucide lucide-panel-left-open h-5 w-5"><rect width="18" height="18" x="3" y="3" rx="2"></rect><path d="M9 3v18"></path><path d="m14 9 3 3-3 3"></path></svg>
    );

    const renderContent = () => {
        switch (activeSection) {
            case 'pedidos':
                return (
                    <div className="div-dashboard">
                        <h1>Listado de Pedidos (FORMULARIO COD)</h1>
                        <div className="search-button-container">
                            <button className="my-button" onClick={openModal}>+ Nuevo Pedido</button>
                            <div className="search-input-container">
                                <input
                                    type="text"
                                    placeholder="Busca tu orden"
                                    value={searchTerm}
                                    onChange={(e) => setSearchTerm(e.target.value)}
                                    className="search-input"
                                />
                                <img src="/images/search.png" alt="Buscar" className="search-icon" />
                                <select className="filter-select" value={filterOptions.orden} onChange={(e) => setFilterOptions({ ...filterOptions, orden: e.target.value })}>
                                    <option value="">Confirmado</option>
                                    <option value="">Seguimiento</option>
                                    <option value="">Importado</option>
                                    <option value="">Anulado</option>
                                    <option value="">No contesta</option>
                                    <option value="">Contactado</option>
                                    <option value="">Repetido</option>
                                    <option value="">Sin stock</option>
                                </select>
                                <select className="filter-select" value={filterOptions.delivery} onChange={(e) => setFilterOptions({ ...filterOptions, delivery: e.target.value })}>
                                    <option value="Pendiente">Pendiente</option>
                                    <option value="En proceso">En proceso</option>
                                    <option value="Listo para envío">Listo para envío</option>
                                    <option value="En tránsito">En tránsito</option>
                                    <option value="En reparto">En reparto</option>
                                    <option value="Entregado">Entregado</option>
                                    <option value="Intento de entrega fallido">Intento de entrega fallido</option>
                                    <option value="Devuelto">Devuelto</option>
                                    <option value="Cancelado">Cancelado</option>
                                </select>
                                <select className="filter-select" value={filterOptions.tranzabilidad} onChange={(e) => setFilterOptions({ ...filterOptions, tranzabilidad: e.target.value })}>
                                    <option value="">Tranzabilidad</option>
                                </select>
                                <select className="filter-select" value={filterOptions.importes} onChange={(e) => setFilterOptions({ ...filterOptions, importes: e.target.value })}>
                                    <option value="">Importes</option>
                                </select>
                                <input
                                    type="date"
                                    className="filter-date"
                                    value={filterOptions.fechaInicio}
                                    onChange={(e) => setFilterOptions({ ...filterOptions, fechaInicio: e.target.value })}
                                />
                                <input
                                    type="date"
                                    className="filter-date"
                                    value={filterOptions.fechaFin}
                                    onChange={(e) => setFilterOptions({ ...filterOptions, fechaFin: e.target.value })}
                                />
                            </div>
                        </div>
                        <div className="lista-clientes">
                            <table>
                                <thead>
                                    <tr>
                                        <th className="text-center">Orden</th>
                                        <th className="text-center">Acciones</th>
                                        <th className="text-center">Delivery</th>
                                        <th className="text-center">Tranzabilidad</th>
                                        <th className="text-center">Importes</th>
                                        <th className="text-center">Pagos</th>
                                        <th className="text-center">Productos</th>
                                        <th className="text-center">Nota</th>
                                        <th className="text-center">Cliente</th>
                                        <th className="text-center">Ubicación</th>
                                        <th className="text-center">Fechas</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    {clients.map((client, index) => (
                                        <tr key={index}>
                                            <td className="text-center"></td>
                                            <td className="text-center">
                                                <button className="btn-editar" onClick={() => handleEdit(index)}>Editar</button>
                                                <button className="btn-eliminar" onClick={() => handleDelete(index)}>Eliminar</button>
                                            </td>
                                            <td className="text-center"></td>
                                            <td className="text-center"></td>
                                            <td className="text-center"></td>
                                            <td className="text-center"></td>
                                            <td className="text-center">{client.producto}</td>
                                            <td className="text-center">{client.notas}</td>
                                            <td className="text-center">{client.nombre}</td>
                                            <td className="text-center">{client.distrito?.texto}</td>
                                            <td className="text-center">
                                                <div>
                                                    Ingreso: {client.diaIngreso}<br />
                                                    Atención: {client.diaAtencion}<br />
                                                    Programado: {client.diaProgramado}
                                                </div>
                                            </td>
                                        </tr>
                                    ))}
                                </tbody>
                            </table>
                        </div>
                    </div>
                );
            case 'pedidos2':
                return <PedidosDashboard />;
            case 'productos':
                return <div>Contenido de Productos</div>;
            case 'categorias':
                return <div>Contenido de Categorías</div>;
            case 'almacen':
                return <div>Contenido de Almacén</div>;
            case 'devolucion':
                return <div>Contenido de Devolución</div>;
            case 'reparto':
                return <div>Contenido de Reparto</div>;
            case 'seguimiento':
                return <div>Contenido de Seguimiento</div>;
            case 'calendario':
                return <div>Contenido de Calendario</div>;
            default:
                return <div className="welcome-dashboard">Bienvenido al Panel de Control. Selecciona una opción del menú para comenzar.</div>;
        }
    };

    return (
        <div className="dashboard-container">
            <header className="dashboard-header">
                <div className={`panel-control-header ${sidebarCollapsed ? 'sidebar-collapsed' : ''}`}>
                    <button onClick={toggleSidebar} className="sidebar-toggle-button">
                        {sidebarCollapsed ? openIcon : openCloseIcon}
                    </button>
                    <h2>Panel Control</h2>
                    <img src="/images/right arrow.png" alt="Icono Panel Control" className="panel-control-icon" />
                    <h3>Orden de Pedido</h3>
                </div>
                <button className="bell-button">
                    <svg fill="none" stroke="currentColor" viewBox="0 0 24 24" className="header-icon notificaciones-icon"><path d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9" strokeLinecap="round" strokeLinejoin="round" strokeWidth="2"></path></svg>
                </button>
            </header>
            
            <div className="dashboard-content">
                {renderContent()}
            </div>

            <Modal
                isOpen={modalIsOpen}
                onRequestClose={closeModal}
                contentLabel="Nueva Orden"
                className="modal"
                overlayClassName="overlay"
            >
                <div className="modal-header">
                    <div className="modal-close" onClick={closeModal}><img src={closeIcon} alt="Cerrar" /></div>
                    <div className="modal-title">Agregar orden</div>
                </div>
                <h2>Nueva orden</h2>
                <form onSubmit={handleRegister}>
                    <div className="modal-content">
                        <label>Nota:</label>
                        <input type="text" name="nota" value={newClient.nota} onChange={handleInputChange} />

                        <label>Canal:</label>
                        <select name="canal" value={newClient.canal} onChange={handleInputChange}>
                            <option value="Shopify">Shopify</option>
                            {/* Agrega más opciones si es necesario */}
                        </select>

                        <h2>Cliente</h2>
                        <label>Nombres y Apellidos:</label>
                        <input type="text" name="nombresApellidos" value={newClient.nombresApellidos} onChange={handleInputChange} />

                        <label>Móvil:</label>
                        <input type="text" name="movil" value={newClient.movil} onChange={handleInputChange} />

                        <h2>Entrega</h2>
                        <label>Departamento:</label>
                        <select name="departamento" value={newClient.departamento} onChange={handleInputChange}>
                            {/* Agrega opciones de departamentos */}
                        </select>

                        <label>Provincia:</label>
                        <select name="provincia" value={newClient.provincia} onChange={handleInputChange}>
                            {/* Agrega opciones de provincias */}
                        </select>

                        <label>Distrito:</label>
                        <select name="distrito" value={newClient.distrito} onChange={handleInputChange}>
                            {/* Agrega opciones de distritos */}
                        </select>

                        <label>Dirección:</label>
                        <input type="text" name="direccion" value={newClient.direccion} onChange={handleInputChange} />

                        <label>Referencia:</label>
                        <input type="text" name="referencia" value={newClient.referencia} onChange={handleInputChange} />

                        <label>GPS: Latitud, Longitud</label>
                        <input type="text" name="gps" value={newClient.gps} onChange={handleInputChange} />

                        <p>GPS: Solicítalo al cliente por WhatsApp o ver TUTORIAL</p>
                    </div>
                    <div className="modal-buttons">
                        <button type="submit">Guardar</button>
                        <button onClick={closeModal}>Cancelar</button>
                    </div>
                </form>
            </Modal>

            <div className={`dashboard-sidebar ${sidebarCollapsed ? 'collapsed' : ''}`}>
                <div className="sidebar-header">
                    <div className="imagen-header">
                        <img className="img-logo" src="../images/ovedisimos-dashboard.png" alt="Imagen de login" />
                    </div>
                </div>
                <nav>
                    <ul className="main-menu">
                        <li className={`main-menu-item ${expanded.panel ? 'expanded' : ''}`}>
                            <div className="menu-item-header" onClick={() => toggleSection('panel')}>
                                <img
                                    src={gearImages.panel}
                                    alt="Panel Control"
                                    style={{ width: '22px', height: '22px' }}
                                />
                                <i className="fas fa-shopping-cart"></i>
                                <span style={{ fontWeight: 400, fontSize: 18, color: spanColors.panel }}>Panel Control</span>
                                <i className={`fas fa-chevron-${expanded.panel ? 'down' : 'right'}`}></i>
                                <img
                                    src={arrowImages.panel}
                                    alt="Panel Control"
                                    className="menu-icon"
                                />
                            </div>
                            {expanded.panel && (
                                <ul className="submenu">
                                    <li 
                                        onClick={() => handleSectionClick('pedidos')} 
                                        className={activeSection === 'pedidos' ? 'active' : ''}
                                    >
                                        Pedidos
                                    </li>
                                    <li 
                                        onClick={() => handleSectionClick('pedidos2')} 
                                        className={activeSection === 'pedidos2' ? 'active' : ''}
                                    >
                                        Pedidos 2
                                    </li>
                                    <li 
                                        onClick={() => handleSectionClick('productos')} 
                                        className={activeSection === 'productos' ? 'active' : ''}
                                    >
                                        Productos
                                    </li>
                                    <li 
                                        onClick={() => handleSectionClick('categorias')} 
                                        className={activeSection === 'categorias' ? 'active' : ''}
                                    >
                                        Categorias
                                    </li>
                                    <li 
                                        onClick={() => handleSectionClick('almacen')} 
                                        className={activeSection === 'almacen' ? 'active' : ''}
                                    >
                                        Almacen
                                    </li>
                                    <li 
                                        onClick={() => handleSectionClick('devolucion')} 
                                        className={activeSection === 'devolucion' ? 'active' : ''}
                                    >
                                        Devolución
                                    </li>
                                    <li 
                                        onClick={() => handleSectionClick('reparto')} 
                                        className={activeSection === 'reparto' ? 'active' : ''}
                                    >
                                        Reparto
                                    </li>
                                    <li 
                                        onClick={() => handleSectionClick('seguimiento')} 
                                        className={activeSection === 'seguimiento' ? 'active' : ''}
                                    >
                                        Seguimiento
                                    </li>
                                    <li 
                                        onClick={() => handleSectionClick('calendario')} 
                                        className={activeSection === 'calendario' ? 'active' : ''}
                                    >
                                        Calendario
                                    </li>
                                </ul>
                            )}
                        </li>
                        <li className={`main-menu-item ${expanded.clientes ? 'expanded' : ''}`}>
                            <div className="menu-item-header" onClick={() => toggleSection('clientes')}>
                                <img
                                    src={gearImages.clientes}
                                    alt="Clientes"
                                    style={{ width: '22px', height: '22px' }}
                                />
                                <i className="fas fa-users"></i>
                                <span style={{ fontWeight: 400, fontSize: 18, color: spanColors.clientes }}>Clientes</span>
                                <i className={`fas fa-chevron-${expanded.clientes ? 'down' : 'right'}`}></i>
                                <img
                                    src={arrowImages.clientes}
                                    alt="Clientes"
                                    className="menu-icon"
                                />
                            </div>
                            {expanded.clientes && <ul className="submenu">
                                <li>Cliente 1</li>
                                <li>Cliente 2</li>
                            </ul>}
                        </li>
                        <li className={`main-menu-item ${expanded.motorizados ? 'expanded' : ''}`}>
                            <div className="menu-item-header" onClick={() => toggleSection('motorizados')}>
                                <img
                                    src={gearImages.motorizados}
                                    alt="Motorizados"
                                    style={{ width: '22px', height: '22px' }}
                                />
                                <i className="fas fa-motorcycle"></i>
                                <span style={{ fontWeight: 400, fontSize: 18, color: spanColors.motorizados }}>Motorizados</span>
                                <i className={`fas fa-chevron-${expanded.motorizados ? 'down' : 'right'}`}></i>
                                <img
                                    src={arrowImages.motorizados}
                                    alt="Motorizados"
                                    className="menu-icon"
                                />
                            </div>
                            {expanded.motorizados && <ul className="submenu">
                                <li>Motorizado 1</li>
                                <li>Motorizado 2</li>
                            </ul>}
                        </li>
                        <li className={`main-menu-item ${expanded.asesores ? 'expanded' : ''}`}>
                            <div className="menu-item-header" onClick={() => toggleSection('asesores')}>
                                <img
                                    src={gearImages.asesores}
                                    alt="Asesores"
                                    style={{ width: '22px', height: '22px' }}
                                />
                                <i className="fas fa-user-tie"></i>
                                <span style={{ fontWeight: 400, fontSize: 18, color: spanColors.asesores }}>Asesores</span>
                                <i className={`fas fa-chevron-${expanded.asesores ? 'down' : 'right'}`}></i>
                                <img
                                    src={arrowImages.asesores}
                                    alt="Asesores"
                                    className="menu-icon"
                                />
                            </div>
                            {expanded.asesores && <ul className="submenu">
                                <li>Asesor 1</li>
                                <li>Asesor 2</li>
                            </ul>}
                        </li>
                        <li className={`main-menu-item ${expanded.reportes ? 'expanded' : ''}`}>
                            <div className="menu-item-header" onClick={() => toggleSection('reportes')}>
                                <img
                                    src={gearImages.reportes}
                                    alt="Reporte"
                                    style={{ width: '22px', height: '22px' }}
                                />
                                <i className="fas fa-user-tie"></i>
                                <span style={{ fontWeight: 400, fontSize: 18, color: spanColors.reportes }}>Reportes</span>
                                <i className={`fas fa-chevron-${expanded.reportes ? 'down' : 'right'}`}></i>
                                <img
                                    src={arrowImages.reportes}
                                    alt="Reportes"
                                    className="menu-icon"
                                />
                            </div>
                            {expanded.reportes && <ul className="submenu">
                                <li>Reporte 1</li>
                                <li>Reporte 2</li>
                            </ul>}
                        </li>
                    </ul>
                </nav>
                <img src="/images/idea.png" alt="Idea" className="floating-idea-icon" />
            </div>
        </div>
    );
}

export default Dashboard;

//dashboard.css
body {
  margin: 0;
  padding: 0;
  background-color: #fff;
}

.dashboard-container {
  font-family: sans-serif;
  background-color: #fff;
  width: 100%; 
}

.dashboard-sidebar {
  background-color: #141e58; 
  color: #fff;
  width: 240px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  z-index: 1; 
  position: fixed; 
  top: 0; 
  left: 0; 
  height: 100vh; 
  transition: left 0.3s ease;
}

.dashboard-sidebar.collapsed {
left: -280px; 
}

.sidebar-toggle-icon {
position: absolute;
top: 90px;
right: -13px; 
width: 30px;
height: 30px;
cursor: pointer;
background-color: #4763e4;
display: flex;
justify-content: center;
align-items: center;
}

.menu-icon-small {
width: 40px;
height: 40px;
}

.dashboard-sidebar {
background-color: #141e58; 
color: #fff;
width: 240px;
padding: 20px;
display: flex;
flex-direction: column;
z-index: 1; 
position: fixed; 
top: 0; 
left: 0; 
height: 100vh; 
}

.dashboard-content {
margin-left: 280px; 
width: calc(100% - 280px); 
padding: 20px;
box-sizing: border-box;
display: flex;
justify-content: center;
}

.dashboard-header {
  background-color: #021428;
  color: #4763e4;
  padding: 5px;
  text-align: center;
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: padding-left 0.3s ease; 
}

.panel-control-header {
display: flex;
align-items: center;
transition: margin-left 0.3s ease; 
}

.panel-control-header.sidebar-collapsed {
margin-left: -290px; 
}

.panel-control-icon {
width: 32px; 
height: 32px; 
margin-left: 15px; 
margin-top: 0px;
}

.panel-control-header h2 {
  margin-left: 380px;
  color: #eeeeee;
  font-size: 20px;
  font-weight: 400;
}

.panel-control-header h3 {
  margin-left: 10px;
  color: #eeeeee;
  font-size: 20px;
  font-weight: 400;
}

.sidebar-toggle-button {
background-color: #021428;
margin-left: 300px; 
display: flex;
align-items: center;
justify-content: center;
border: none;
width: 40px;
height: 40px;
border-radius: 10px;
cursor: pointer;
transition: all 0.3s ease; 
position: absolute; 
}

.panel-control-header svg {
color: #eeeeee;
border-radius: 10px;
}

.dashboard-sidebar.collapsed {
left: -280px;
}

.sidebar-toggle-button:hover {
background-color: #05305f;
}

.bell-button {
background-color: #021428;
margin-right: 30px;
display: flex; 
align-items: center; 
justify-content: center; 
border:none;
width: 40px;
height: 40px;
border-radius: 12px;
cursor: pointer;
}

.bell-button:hover {
background-color: #05305f;
}

.bell-button svg {
width: 30px;
height: 30px;
color: #eeeeee;
cursor: pointer;
}
.sidebar-header {
display: flex;
align-items: center;
margin-bottom: 20px;
}

.sidebar-header i {
margin-right: 10px;
}

.main-menu {
list-style: none;
padding: 0;
}

.main-menu-item {
margin-bottom: 10px;
}

.menu-item-header {
  display: flex;
  align-items: center;
  padding: 10px;
  cursor: pointer;
  margin-bottom: 10px;
  border-radius: 10px;
  justify-content: flex-start;
}

.menu-icon {
  margin-left: auto;
}

.menu-item-header img {
  margin-right: 5px;
  height: 24px;
  width: 24px;
}

.menu-item-header span {
  margin: 0;
  padding-right: 10px; 
}

.menu-item-header i {
margin-right: 20px;
}

.submenu {
list-style: none;
padding: 0;
padding-left: 10px;
padding-right: 10px;
}

.submenu li {
  padding: 8px;
  margin-bottom: 5px;
  font-size: 18px;
  padding-left: 50px;
}

.submenu li.activo {
  background-color: #4256d0;
  border-radius: 12px;
}

.submenu li:hover{
  background-color: #4256d0; 
  border-radius: 12px;
  padding-left: 50px;
  cursor: pointer;
}
.imagen-header{
  background-color: #141e58; 
  margin-left: -20px;
  padding: 20px;
  margin-top: -20px;
  margin-right: -20px;
}
.imagen-header img {
width: 100%; 
height: 100%;
}

.div-dashboard {
background-color: #fff;
padding: 20px;
width: 100%;
max-width: 1600px; 
margin: 0 auto; 
transition: margin-left 0.3s ease 
}

.div-dashboard.sidebar-collapsed {
  margin-left: -300px;
}

.div-dashboard h1{
margin-top: 30px;
font-size: 28px; 
margin-bottom: 30px;
text-align: center; 
} 

.search-button-container {
display: flex;
flex-direction: row;
flex-wrap: nowrap; 
justify-content: space-between; 
align-items: center;
margin-bottom: 25px;
gap: 12px;
position: relative;
}

.my-button {
padding: 8px 16px;
background-color: #4763e4;
color: #fff;
border: 1px solid #4c67e4;
border-radius: 25px;
cursor: pointer;
font-size: 14px;
white-space: nowrap;
order: -1; 
margin-right: 10px;
}

.search-input-container {
position: relative;
display: inline-block;
order: 0;
}

.search-input {
padding: 6px;
border: 2px solid #f4f4f4;
border-color: #a3b1f1; 
border-radius: 20px;
width: 230px; 
height: 20px;
font-size: 14px;
padding-left: 35px; 
font-weight: 300; 
margin: 0;
}

.search-input:focus {
outline: none;
box-shadow: 0 0 5px rgba(71, 99, 228, 0.5); 
}

.search-icon {
position: absolute;
left: 12px; 
top: 49%;
transform: translateY(-50%);
width: 18px; 
height: 18px;
}
.my-button {
  padding: 8px 16px;
  background-color: #4763e4;
  color: #fff;
  border: 1px solid #4c67e4;
  border-radius: 25px;
  cursor: pointer;
  font-size: 14px;
  white-space: nowrap;
}
.filter-select {
padding: 10px;
padding-left: 20px;
border: 1px solid #dddddd;
border-radius: 30px;
font-family: Arial, sans-serif;
font-size: 14px;
box-sizing: border-box;
width: 175px;
appearance: none;
background-color: white;
margin: 0;
height: 36px;
}

.filter-select:focus {
border: 1px solid #f4f4f4;
border-color: #a3b1f1; 
outline: none;
}

.filter-date {
padding: 8px 15px;
border-radius: 30px;
border: 1px solid #ccc;
width: 120px;
font-family: Arial, sans-serif;
margin: 0;
height: 36px;
}

.lista-clientes {
margin: 0 auto; 
width: 100%;
height: auto;
max-height: 800px;
padding: 10px;
border-radius: 12px;
overflow-x: auto; 
overflow-y: auto;
font-size: 16px; 
color: #a1a1aa;
}

.lista-clientes table {
width: 100%;
border-collapse: collapse; 
}

.lista-clientes table th.text-center {
border-right: 1px solid #ddd; 
}

.lista-clientes table th.text-center:last-child {
border-right: none; 
}

.lista-clientes th,
.lista-clientes td {
padding: 12px 8px; 
text-align: left;
border-bottom: 1px solid #ddd; 
}

.lista-clientes td {
font-size: 14px;
color: black;
}

.lista-clientes th {
background-color: #fbfbfb; 
font-weight: 400;
color: #686868;
}

.lista-clientes tbody tr:hover {
background-color: #f9f9f9; 
}

.lista-clientes td button {
margin-right: 5px;
padding: 5px 10px;
border: 1px solid #ccc;
border-radius: 4px;
cursor: pointer;
}

.lista-clientes td button:hover {
background-color: #f0f0f0;
}

.lista-clientes td button.btn-editar {
background-color: white;
color: #5c73db;
border: none;
padding: 8px 16px;
text-align: center;
text-decoration: none;
display: inline-block;
font-size: 14px;
border-radius: 10px;
border: 1px solid #4c67e4;
cursor: pointer;
margin-left: 50px;
}

.lista-clientes td button.btn-eliminar {
background-color: #dc2626;
color: white;
border: none;
padding: 8px 16px;
text-align: center;
text-decoration: none;
display: inline-block;
font-size: 14px;
border-radius: 10px;
cursor: pointer;
margin-left: 5px;
}

.lista-clientes th.text-center,
.lista-clientes td.text-center {
text-align: center;
}

.lista-clientes td.lista-clientes-acciones {
border-bottom: none;
text-align: left;
}

.estado-div {
padding: 8px 14px;
border-radius: 20px;
display: inline-block; 
}

.estado-div.estado-enCamino {
background-color: #ffedd5;
}

.estado-div.estado-completado {
background-color: #dcfce7;
}

.estado-div.estado-cancelado {
background-color: #fee2e2;
}

table .estado-enCamino {
color: #9a3412;
font-weight: 400;
}

table .estado-completado {
color: #166534;
font-weight: 400;
}

table .estado-cancelado {
color: #991b1b;
font-weight: 400;
}

.floating-idea-icon {
position: fixed;
bottom: 20px; 
right: 20px; 
width: 50px; 
height: 50px; 
z-index: 1;
cursor: pointer;
}

//pedidosdashboard.jsx 
import React, { useState } from 'react';
import { 
  Box, Button, TextField, InputAdornment, Table, TableBody, TableCell, 
  TableContainer, TableHead, TableRow, Paper, FormControl, Select, MenuItem,
  IconButton, Typography, Chip, Drawer, Divider, Radio, RadioGroup, FormControlLabel
} from '@mui/material';
import { Search, WhatsApp, FilterList, MusicNote, Instagram, Close, Add, Save } from '@mui/icons-material';
import './PedidosDashboard.css';

const EstadoChip = ({ estado, estadoAdicional }) => {
  const colorMap = {
    'IN-WOW': '#3884f7',
    'ADMITIDO': '#10b981',
    'POR DERIVAR': '#f59e0b',
    'FINAL DE ENTREGA': '#8b5cf6',
    'default': '#4763e4'
  };
  
  return (
    <Box sx={{ display: 'flex', flexDirection: 'column', gap: 1 }}>
      <Chip 
        label={estado} 
        sx={{ bgcolor: '#4763e4', color: 'white', borderRadius: '4px', fontWeight: 'bold', fontSize: '0.75rem' }} 
      />
      {estadoAdicional && (
        <Chip 
          label={estadoAdicional} 
          sx={{ bgcolor: colorMap[estadoAdicional] || colorMap.default, color: 'white', borderRadius: '4px', fontWeight: 'bold', fontSize: '0.75rem' }} 
        />
      )}
    </Box>
  );
};

const FechaItem = ({ label, fecha }) => (
  <Box sx={{ display: 'flex', gap: 1 }}>
    <Typography variant="caption" sx={{ color: '#6b7280' }}>{label}:</Typography>
    <Typography variant="caption">{fecha}</Typography>
  </Box>
);

function PedidosDashboard() {
  const [filtros, setFiltros] = useState({
    estado: 'CONFIRMADO',
    estadoEntrega: '',
    almacen: 'TODOS',
    fechaInicio: '2023-03-12',
    fechaFin: '2023-03-27',
    searchTerm: ''
  });
  
  const [drawerOpen, setDrawerOpen] = useState(false);
  
  const estadoInicial = {
    cliente: '',
    telefono: '',
    direccion: '',
    distrito: '',
    productos: [],
    estado: 'CONFIRMADO',
    estadoAdicional: 'IN-WOW',
    medioPago: '',
    total: '',
    notaAdicional: ''
  };
  
  const [nuevoPedido, setNuevoPedido] = useState(estadoInicial);
  const [nuevoProducto, setNuevoProducto] = useState({ descripcion: '', cantidad: 1, precio: '' });

  const [pedidos] = useState([
    {
      id: 'NOW004351',
      cliente: 'Denisse Peña Obregon Erika',
      ubicacion: 'Jr piñac mz.u lt 3-3 - Lurigancho - Ate',
      estado: 'CONFIRMADO',
      estadoAdicional: 'IN-WOW',
      importes: {
        total: 'S/ 121.00',
        detalles: [
          { descripcion: '1 KIT DE 12 BOLSIGATOS PARA UÑAS', valor: '' },
          { descripcion: '2 CAJA DE 10 PARCHES DESINTOXICANTES', valor: '' },
          { descripcion: '1 PRODUCTO SORPRESA DE REGALO', valor: '' }
        ]
      },
      fechas: {
        registro: '27/03/2023',
        despacho: '27/03/2023',
        entrega: '28/03/2023'
      }
    }
  ]);

  const handleFiltroChange = (campo, valor) => {
    setFiltros({ ...filtros, [campo]: valor });
  };

  const handleFormChange = (e) => {
    setNuevoPedido({ ...nuevoPedido, [e.target.name]: e.target.value });
  };
  
  const handleProductoChange = (e) => {
    setNuevoProducto({ ...nuevoProducto, [e.target.name]: e.target.value });
  };

  const agregarProducto = () => {
    if (nuevoProducto.descripcion && nuevoProducto.precio) {
      setNuevoPedido({
        ...nuevoPedido,
        productos: [...nuevoPedido.productos, {
          descripcion: `${nuevoProducto.cantidad} ${nuevoProducto.descripcion}`,
          valor: `S/ ${nuevoProducto.precio}`
        }]
      });
      setNuevoProducto({ descripcion: '', cantidad: 1, precio: '' });
    }
  };

  const guardarPedido = () => {
    setDrawerOpen(false);
    setNuevoPedido(estadoInicial);
  };

  const pedidosFiltrados = pedidos.filter(pedido => {
    const { estado, estadoEntrega, searchTerm } = filtros;
    if (estado && pedido.estado !== estado) return false;
    if (estadoEntrega && pedido.estadoAdicional !== estadoEntrega) return false;
    if (searchTerm && !pedido.cliente.toLowerCase().includes(searchTerm.toLowerCase()) && 
        !pedido.id.toLowerCase().includes(searchTerm.toLowerCase())) return false;
    return true;
  });

  return (
    <Box sx={{ p: 3, bgcolor: '#f9fafb', minHeight: '100vh', width: '100%', boxSizing: 'border-box', overflowX: 'auto' }}>
      {/* Header */}
      <Box sx={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', mb: 2 }}>
        <Typography variant="h5" sx={{ fontWeight: 'bold' }}>Pedidos</Typography>
        <Box sx={{ display: 'flex', gap: 2 }}>
          {[MusicNote, Instagram, WhatsApp].map((Icon, idx) => (
            <IconButton key={idx} color="primary"><Icon /></IconButton>
          ))}
        </Box>
      </Box>

      {/* Filtros */}
      <Box sx={{ display: 'flex', alignItems: 'center', mb: 3, gap: 2, flexWrap: 'wrap' }}>
        <Button 
          variant="contained" 
          sx={{ bgcolor: '#4f46e5', borderRadius: '20px', '&:hover': { bgcolor: '#4338ca' } }}
          onClick={() => setDrawerOpen(true)}
          startIcon={<Add />}
        >
          Nuevo Pedido
        </Button>

        <TextField
          placeholder="Buscar..."
          variant="outlined"
          size="small"
          value={filtros.searchTerm}
          onChange={(e) => handleFiltroChange('searchTerm', e.target.value)}
          sx={{ minWidth: 250, bgcolor: 'white' }} 
          InputProps={{ startAdornment: (<InputAdornment position="start"><Search /></InputAdornment>) }}
        />

        <FormControl size="small" sx={{ minWidth: 150, bgcolor: 'white' }}>
          <Select
            value={filtros.estado}
            onChange={(e) => handleFiltroChange('estado', e.target.value)}
            sx={{ height: 40 }}
          >
            {['CONFIRMADO', 'PENDIENTE', 'CANCELADO'].map(opt => (
              <MenuItem key={opt} value={opt}>{opt}</MenuItem>
            ))}
          </Select>
        </FormControl>

        <FormControl size="small" sx={{ minWidth: 150, bgcolor: 'white' }}>
          <Select
            value={filtros.estadoEntrega}
            onChange={(e) => handleFiltroChange('estadoEntrega', e.target.value)}
            renderValue={selected => selected || "Selecciona estados"}
            sx={{ height: 40 }}
          >
            <MenuItem value="">Todos</MenuItem>
            {['IN-WOW', 'ADMITIDO', 'POR DERIVAR', 'FINAL DE ENTREGA'].map(opt => (
              <MenuItem key={opt} value={opt}>{opt}</MenuItem>
            ))}
          </Select>
        </FormControl>

        <FormControl size="small" sx={{ minWidth: 150, bgcolor: 'white' }}>
          <Select
            value={filtros.almacen}
            onChange={(e) => handleFiltroChange('almacen', e.target.value)}
            sx={{ height: 40 }}
          >
            {['TODOS', 'LIMA', 'PROVINCIA'].map(opt => (
              <MenuItem key={opt} value={opt}>{opt}</MenuItem>
            ))}
          </Select>
        </FormControl>

        <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
          <Typography variant="body2" sx={{ whiteSpace: 'nowrap' }}>Filtrar por:</Typography>
          <FormControl size="small" sx={{ minWidth: 180, bgcolor: 'white' }}>
            <Select value="fecha" sx={{ height: 40 }}>
              <MenuItem value="fecha">Fecha Actualización</MenuItem>
              <MenuItem value="creacion">Fecha Creación</MenuItem>
            </Select>
          </FormControl>
        </Box>

        <Box sx={{ display: 'flex', gap: 1 }}>
          {['fechaInicio', 'fechaFin'].map(campo => (
            <TextField
              key={campo}
              type="date"
              size="small"
              value={filtros[campo]}
              onChange={(e) => handleFiltroChange(campo, e.target.value)}
              sx={{ width: 160, bgcolor: 'white' }}
            />
          ))}
        </Box>

        <Button 
          variant="outlined"
          startIcon={<FilterList />}
          sx={{ bgcolor: 'white', borderColor: '#4763e4', color: '#4763e4' }}
        >
          Más filtros
        </Button>
      </Box>

      {/* Tabla de Pedidos */}
      <TableContainer component={Paper} sx={{ mb: 4, boxShadow: '0 4px 6px -1px rgb(0 0 0 / 0.1)' }}>
        <Table sx={{ minWidth: 650 }} size="small">
          <TableHead>
            <TableRow sx={{ bgcolor: '#f3f4f6' }}>
              {['Order', 'Actions', 'Delivery', 'Trazabilidad', 'Importes', 'Pagos', 'Products', 'Nota', 'Cliente', 'Ubicación', 'Fechas']
                .map(header => <TableCell key={header} sx={{ fontWeight: 'bold' }}>{header}</TableCell>)}
            </TableRow>
          </TableHead>
          <TableBody>
            {pedidosFiltrados.map((pedido) => (
              <TableRow key={pedido.id} sx={{ '&:hover': { bgcolor: '#f9fafb' } }}>
                <TableCell>{pedido.id}</TableCell>
                <TableCell>
                  <Box sx={{ display: 'flex', gap: 1 }}>
                    <IconButton size="small" sx={{ color: '#10b981' }}><WhatsApp fontSize="small" /></IconButton>
                    <IconButton size="small" sx={{ color: '#6b7280' }}><Search fontSize="small" /></IconButton>
                  </Box>
                </TableCell>
                <TableCell><EstadoChip estado={pedido.estado} estadoAdicional={pedido.estadoAdicional} /></TableCell>
                <TableCell><IconButton size="small" sx={{ color: '#f59e0b' }}><FilterList fontSize="small" /></IconButton></TableCell>
                <TableCell>
                  <Box>{pedido.importes.total}
                    <Box sx={{ color: '#6b7280', fontSize: '0.75rem' }}>
                      {pedido.importes.detalles.map((detalle, idx) => (
                        <Typography key={idx} variant="caption" display="block">{detalle.descripcion}</Typography>
                      ))}
                    </Box>
                  </Box>
                </TableCell>
                <TableCell>-</TableCell>
                <TableCell sx={{ color: '#6b7280', fontSize: '0.75rem' }}>
                  {pedido.importes.detalles.map((detalle, idx) => (
                    <Typography key={idx} variant="caption" display="block">{detalle.descripcion}</Typography>
                  ))}
                </TableCell>
                <TableCell>-</TableCell>
                <TableCell sx={{ maxWidth: 150 }}><Typography noWrap>{pedido.cliente}</Typography></TableCell>
                <TableCell sx={{ maxWidth: 200 }}><Typography variant="body2" noWrap>{pedido.ubicacion}</Typography></TableCell>
                <TableCell>
                  <Box sx={{ fontSize: '0.75rem' }}>
                    <FechaItem label="R" fecha={pedido.fechas.registro} />
                    <FechaItem label="D" fecha={pedido.fechas.despacho} />
                    <FechaItem label="E" fecha={pedido.fechas.entrega} />
                  </Box>
                </TableCell>
              </TableRow>
            ))}
          </TableBody>
        </Table>
      </TableContainer>

      <Drawer
        anchor="right"
        open={drawerOpen}
        onClose={() => setDrawerOpen(false)}
        sx={{ '& .MuiDrawer-paper': { width: '500px', boxSizing: 'border-box', padding: 3 } }}
      >
        <Box sx={{ width: '100%' }}>
          <Box sx={{ display: 'flex', justifyContent: 'space-between', mb: 2 }}>
            <Typography variant="h6" fontWeight="bold">Nuevo Pedido</Typography>
            <IconButton onClick={() => setDrawerOpen(false)}><Close /></IconButton>
          </Box>
          
          <Divider sx={{ mb: 3 }} />
          
          <Box component="form" sx={{ display: 'flex', flexDirection: 'column', gap: 2 }}>
            <Typography variant="subtitle1" fontWeight="bold">Información del Cliente</Typography>
            
            {['cliente', 'telefono', 'direccion', 'distrito'].map(field => (
              <TextField
                key={field}
                label={field.charAt(0).toUpperCase() + field.slice(1)}
                name={field}
                value={nuevoPedido[field]}
                onChange={handleFormChange}
                fullWidth
                size="small"
              />
            ))}
            
            <Typography variant="subtitle1" fontWeight="bold" sx={{ mt: 1 }}>Estado del Pedido</Typography>
            
            <FormControl component="fieldset">
              <RadioGroup row name="estado" value={nuevoPedido.estado} onChange={handleFormChange}>
                {['CONFIRMADO', 'PENDIENTE', 'CANCELADO'].map(opt => (
                  <FormControlLabel key={opt} value={opt} control={<Radio />} label={opt} />
                ))}
              </RadioGroup>
            </FormControl>
            
            <FormControl fullWidth size="small">
              <Select
                name="estadoAdicional"
                value={nuevoPedido.estadoAdicional}
                onChange={handleFormChange}
              >
                {['IN-WOW', 'ADMITIDO', 'POR DERIVAR', 'FINAL DE ENTREGA'].map(opt => (
                  <MenuItem key={opt} value={opt}>{opt}</MenuItem>
                ))}
              </Select>
            </FormControl>
            
            <Typography variant="subtitle1" fontWeight="bold" sx={{ mt: 1 }}>Productos</Typography>
            
            <Box sx={{ display: 'flex', gap: 1 }}>
              <TextField
                label="Cantidad"
                name="cantidad"
                type="number"
                value={nuevoProducto.cantidad}
                onChange={handleProductoChange}
                size="small"
                sx={{ width: '100px' }}
              />
              <TextField
                label="Descripción"
                name="descripcion"
                value={nuevoProducto.descripcion}
                onChange={handleProductoChange}
                size="small"
                sx={{ flex: 1 }}
              />
              <TextField
                label="Precio"
                name="precio"
                type="number"
                value={nuevoProducto.precio}
                onChange={handleProductoChange}
                size="small"
                sx={{ width: '100px' }}
                InputProps={{ startAdornment: <InputAdornment position="start">S/</InputAdornment> }}
              />
              <Button 
                variant="contained" 
                onClick={agregarProducto}
                sx={{ width: '40px', minWidth: '40px', height: '40px', p: 0 }}
              >
                <Add />
              </Button>
            </Box>
            
            {nuevoPedido.productos.length > 0 && (
              <Paper variant="outlined" sx={{ p: 2, mt: 1 }}>
                <Typography variant="subtitle2" fontWeight="bold" gutterBottom>Productos añadidos:</Typography>
                
                {nuevoPedido.productos.map((producto, index) => (
                  <Box key={index} sx={{ display: 'flex', justifyContent: 'space-between', py: 0.5 }}>
                    <Typography variant="body2">{producto.descripcion}</Typography>
                    <Typography variant="body2">{producto.valor}</Typography>
                  </Box>
                ))}
                
                <Divider sx={{ my: 1 }} />
                
                <Box sx={{ display: 'flex', justifyContent: 'flex-end', mt: 1 }}>
                  <TextField
                    label="Total"
                    name="total"
                    value={nuevoPedido.total}
                    onChange={handleFormChange}
                    size="small"
                    sx={{ width: '100px' }}
                    InputProps={{ startAdornment: <InputAdornment position="start">S/</InputAdornment> }}
                  />
                </Box>
              </Paper>
            )}
            
            <Typography variant="subtitle1" fontWeight="bold" sx={{ mt: 1 }}>Nota Adicional</Typography>
            <TextField
              label="Nota adicional"
              name="notaAdicional"
              value={nuevoPedido.notaAdicional}
              onChange={handleFormChange}
              multiline
              rows={3}
              fullWidth
            />
            
            <Typography variant="subtitle1" fontWeight="bold" sx={{ mt: 1 }}>Medio de Pago</Typography>
            <FormControl component="fieldset">
              <RadioGroup row name="medioPago" value={nuevoPedido.medioPago} onChange={handleFormChange}>
                {['EFECTIVO', 'TRANSFERENCIA', 'YAPE', 'PLIN'].map(opt => (
                  <FormControlLabel key={opt} value={opt} control={<Radio />} label={opt} />
                ))}
              </RadioGroup>
            </FormControl>
            
            <Box sx={{ display: 'flex', justifyContent: 'flex-end', gap: 2, mt: 3 }}>
              <Button variant="outlined" onClick={() => setDrawerOpen(false)}>Cancelar</Button>
              <Button 
                variant="contained" 
                onClick={guardarPedido}
                startIcon={<Save />}
                sx={{ bgcolor: '#4f46e5' }}
              >
                Guardar Pedido
              </Button>
            </Box>
          </Box>
        </Box>
      </Drawer>
    </Box>
  );
}

export default PedidosDashboard;

//pedidosdashboaord.css

.MuiChip-root {
    height: 24px;
    font-size: 12px;
  }
  
  .MuiTableCell-root {
    padding: 8px 16px;
  }
  
  .chip-confirmado {
    background-color: #4763e4;
    color: white;
  }
  
  .chip-in-wow {
    background-color: #3884f7;
    color: white;
  }
  
  .chip-admitido {
    background-color: #10b981;
    color: white;
  }
  
  .chip-por-derivar {
    background-color: #f59e0b;
    color: white;
  }
  
  .chip-final-entrega {
    background-color: #8b5cf6;
    color: white;
  }

