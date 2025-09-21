// ResumeList.js
import React, { useState, useEffect } from 'react';
import axios from 'axios';
import { AgGridReact } from 'ag-grid-react';
import 'ag-grid-community/styles/ag-grid.css';
import 'ag-grid-community/styles/ag-theme-alpine.css';

export default function ResumeList({ onLogout, onEdit, onCreate }) {
  const [resumes, setResumes] = useState([]);
  const [page, setPage] = useState(1);
  const [totalPages, setTotalPages] = useState(1);
  const [filters, setFilters] = useState({ first_name: '', last_name: '', email: '' });

  const fetchResumes = async () => {
    const params = { page, limit: 20, ...filters };
    const res = await axios.get('/api/resumes', { params });
    setResumes(res.data.data);
    setTotalPages(res.data.totalPages);
  };

  useEffect(() => {
    fetchResumes();
  }, [page, filters]);

  const columns = [
    { headerName: 'First Name', field: 'first_name' },
    { headerName: 'Last Name', field: 'last_name' },
    { headerName: 'Email', field: 'email' },
    { headerName: 'Phone', field: 'phone' },
    {
      headerName: 'Actions',
      field: 'actions',
      cellRendererFramework: params => (
        <button onClick={() => onEdit(params.data)}>Edit</button>
      ),
    },
  ];

  return (
    <div>
      <h2>Resumes</h2>
      <button onClick={onCreate}>Create New Resume</button>
      <button onClick={onLogout}>Logout</button>

      <div>
        <input placeholder="First Name" value={filters.first_name} onChange={e => setFilters({...filters, first_name: e.target.value})} />
        <input placeholder="Last Name" value={filters.last_name} onChange={e => setFilters({...filters, last_name: e.target.value})} />
        <input placeholder="Email" value={filters.email} onChange={e => setFilters({...filters, email: e.target.value})} />
        <button onClick={() => setPage(1)}>Filter</button>
      </div>

      <div className="ag-theme-alpine" style={{ height: 400, width: '100%' }}>
        <AgGridReact rowData={resumes} columnDefs={columns} pagination={false} />
      </div>

      <div>
        <button disabled={page <= 1} onClick={() => setPage(page - 1)}>Prev</button>
        <span> Page {page} of {totalPages} </span>
        <button disabled={page >= totalPages} onClick={() => setPage(page + 1)}>Next</button>
      </div>
    </div>
  );
}
