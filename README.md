import Foundation


//1

struct cliente: Hashable{
    private var nombre: String
    private var edad: Int
    private var altura: Double
    
    //get and set de nombre
    func getNombre()-> String{
        return self.nombre
    }
    mutating func setNombre(_ nombre: String){
        self.nombre = nombre
    }
    
    //get and set de edad
    func getEdad()-> Int{
        return self.edad
    }
    mutating func setEdad(_ edad: Int){
        self.edad = edad
    }
    
    //get and set de altura
    func getAltura()-> Double{
        return self.altura
    }
    mutating func setAltura(_ altura: Double){
        self.altura = altura
    }
    
}

struct reservation: Hashable{
    
    //variables
    private var id:Int
    private var nombreDelHotel:String
    private var listaDeClientes: Set<cliente>
    private var duración: Int
    private var precio: Double
    private var desayuno: Bool
    
    init(){
        self.id=0;
        self.nombreDelHotel=""
        self.listaDeClientes=[]
        self.duración=0
        self.precio=0
        self.desayuno=false
    }
    
    //get and set de ID
    func getId()-> Int{
        return self.id
    }
    mutating func setId(_ id: Int){
        self.id = id
    }
    
    //get and set de nombreDelHotel
    func getNombreDelHotel()-> String{
        return self.nombreDelHotel
    }
    mutating func setNombreDelHotel(_ nombreDelHotel: String){
        self.nombreDelHotel = nombreDelHotel
    }
    
    //get and set de listaDeClientes
    func getListaDeClientes()-> Set<cliente>{
        return self.listaDeClientes
    }
    mutating func setListaDeClientes(_ listaDeClientes: Set<cliente>){
        self.listaDeClientes = listaDeClientes
    }
    
    //get and set de duración
    func getDuración()-> Int{
        return self.duración
    }
    mutating func setDuración(_ duración: Int){
        self.duración = duración
    }
    
    //get and set de precio
    func getPrecio()-> Double{
        return self.precio
    }
    mutating func setPrecio(_ precio: Double){
        self.precio = precio
    }
    
    //get and set de desayuno
    func getDesayuno()-> Bool{
        return self.desayuno
    }
    mutating func setDesayuno(_ desayuno: Bool){
        self.desayuno = desayuno
    }
}

enum reservationError: Error{
    case reservaConMismoId
    case clienteYaTieneUnaReserva
    case noSeEncontróReservaDelCliente
}


//2

class HotelReservationManager{
    
    //variable que se usara como contador
    var contador: Int
    
    //variables
    private var listaDeReservas: Set<reservation>
    
    //constructor
    init(){
        self.listaDeReservas = []
        self.contador = 0
        
    }
    
    //método para añadir una reserva
    func añadirReserva(_ listaDeClientes: Set<cliente>,_ duración: Int,_ desayuno: Bool) throws -> reservation{
        //constantes nombre del hotel
        let nombreDelHotel: String = "Luchadores"
        
        //variable reserva y rellenada con los parámetros
        var reserva: reservation = reservation()
        contador+=1
        reserva.setId(contador)
        reserva.setNombreDelHotel(nombreDelHotel)
        reserva.setListaDeClientes(listaDeClientes)
        reserva.setDuración(duración)
        //calculo de precio
        if(desayuno){
            reserva.setPrecio(Double(listaDeClientes.count)*20*Double(duración)*1.25)
        }else{
            reserva.setPrecio(Double(listaDeClientes.count)*20*Double(duración)*1)
        }
        reserva.setDesayuno(desayuno)
        
        
        //método para validar que la reserva es única por ID y cliente
        func validarReservaÚnica() throws {
                try listaDeReservas.forEach {res in
                    if(res.getId() == reserva.getId()){
                        throw reservationError.reservaConMismoId
                    }else{
                        try reserva.getListaDeClientes().forEach{ cli in
                            try listaDeClientes.forEach{ clie in
                                if(clie.getNombre() == cli.getNombre()){
                                    throw reservationError.clienteYaTieneUnaReserva
                                }
                            }
                        }
                    }
                }
        }
        
        //validamos
        do{
            try validarReservaÚnica()
            //añadimos la reserva al listado de reservas
            self.listaDeReservas.insert(reserva)
        }
        catch reservationError.reservaConMismoId{
            print("Ya existe una reserve con el mismo ID")
        }
        catch reservationError.clienteYaTieneUnaReserva{
            print("El cliente ya tiene una reserva echa")
        }
        
        //retornamos la reserva
        return reserva
    }
    
    //método para cancelar una reserva
    func cancelarReserva (_ id: Int) throws {
        //lanzar reservationError si no existe la reserva
        var cancelada: Bool = false
        getListaDeReservas().forEach{ res in
            if(res.getId() == id){
                listaDeReservas.remove(res)
                cancelada = true
            }
        }
        if (cancelada == false){
            throw reservationError.noSeEncontróReservaDelCliente
        }
    }
    
    //método para obtener la lista de reservas actuales
    func getListaDeReservas() -> Set<reservation>{
        return self.listaDeReservas
    }
}
