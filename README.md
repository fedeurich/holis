# holis


using System;
using System.Collections.Generic;
using System.Data.SqlClient;
using _03_Dominio.Entidades;
using _03_Dominio.Repositorios;

namespace _04_PersistenciaDatos
{
    public class UsuarioRepositorioSQLServer : UsuarioRepositorio
    {
        private readonly string connectionString;

        public UsuarioRepositorioSQLServer(string connectionString)
        {
            this.connectionString = connectionString;
        }

        public List<Usuario> listar()
        {
            List<Usuario> usuarios = new List<Usuario>();

            using (SqlConnection conexion = new SqlConnection(connectionString))
            {
                string query = "SELECT Id, Nombre, Email, Clave, FechaNacimiento FROM Usuarios";
                SqlCommand comando = new SqlCommand(query, conexion);

                conexion.Open();
                SqlDataReader reader = comando.ExecuteReader();

                while (reader.Read())
                {
                    Usuario usuario = new Usuario(
                        Guid.Parse(reader["Id"].ToString()),
                        reader["Nombre"].ToString(),
                        reader["Email"].ToString(),
                        reader["Clave"].ToString(),
                        DateTime.Parse(reader["FechaNacimiento"].ToString())
                    );
                    usuarios.Add(usuario);
                }
            }

            return usuarios;
        }

        public void grabar(Usuario usuario)
        {
            using (SqlConnection conexion = new SqlConnection(connectionString))
            {
                string query = "INSERT INTO Usuarios (Id, Nombre, Email, Clave, FechaNacimiento) VALUES (@Id, @Nombre, @Email, @Clave, @FechaNacimiento)";
                SqlCommand comando = new SqlCommand(query, conexion);
                comando.Parameters.AddWithValue("@Id", usuario.Id);
                comando.Parameters.AddWithValue("@Nombre", usuario.Nombre);
                comando.Parameters.AddWithValue("@Email", usuario.Email);
                comando.Parameters.AddWithValue("@Clave", usuario.Clave);
                comando.Parameters.AddWithValue("@FechaNacimiento", usuario.FechaNacimiento);

                conexion.Open();
                comando.ExecuteNonQuery();
            }
        }
    }
}
