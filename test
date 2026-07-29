import React, { useState, useEffect } from 'react';
import { 
  Box, Typography, Container, Grid, Paper, IconButton, 
  Tooltip, Chip, CircularProgress, Divider, Button 
} from '@mui/material';
import { motion, AnimatePresence } from 'framer-motion';

// MUI Icons
import CalendarMonthIcon from '@mui/icons-material/CalendarMonth';
import PictureAsPdfIcon from '@mui/icons-material/PictureAsPdf';
import TableChartIcon from '@mui/icons-material/TableChart';
import ArticleIcon from '@mui/icons-material/Article';
import ViewListIcon from '@mui/icons-material/ViewList';
import CheckCircleIcon from '@mui/icons-material/CheckCircle';
import WarningIcon from '@mui/icons-material/Warning';
import EventBusyIcon from '@mui/icons-material/EventBusy';
import ArrowBackIcon from '@mui/icons-material/ArrowBack';

// --- Date Utility ---
const isLastDayOfMonth = (dateString) => {
  if (!dateString) return false;
  // Parse date safely avoiding UTC timezone shifts
  const [year, month, day] = dateString.split('-').map(Number);
  const date = new Date(year, month - 1, day);
  
  // Get the last day of that specific month
  const lastDayOfMonth = new Date(year, month, 0).getDate();
  return date.getDate() === lastDayOfMonth;
};

// --- Mock API Fetch ---
// Change this to '2026-07-15' to test the non-month-end functional screen
const fetchEtlData = () => new Promise(resolve => setTimeout(() => resolve('2026-07-15'), 1000)); 
const fetchReports = () => new Promise(resolve => setTimeout(() => resolve([
  {
    id: 'YSA',
    title: 'YSA Report',
    description: 'Yield Settlement Analysis',
    runs: [{ runId: 1, status: 'SUCCESS', timestamp: '08:00 AM' }]
  },
  {
    id: 'PNL',
    title: 'PNL Report',
    description: 'Profit & Loss Statement',
    runs: [
      { runId: 1, status: 'FAILED', timestamp: '09:15 AM' },
      { runId: 2, status: 'SUCCESS', timestamp: '10:30 AM' }
    ]
  }
]), 1200));

// --- Sub-Components ---

const DownloadActions = () => {
  const formats = [
    { label: 'PDF', icon: <PictureAsPdfIcon />, color: '#d32f2f' },
    { label: 'Excel', icon: <TableChartIcon />, color: '#1b5e20' },
    { label: 'Text', icon: <ArticleIcon />, color: '#424242' },
    { label: 'PSV', icon: <ViewListIcon />, color: '#0277bd' },
  ];

  return (
    <Box sx={{ display: 'flex', gap: 2, mt: 2, justifyContent: 'center' }}>
      {formats.map((format, index) => (
        <Tooltip title={`Download ${format.label}`} key={format.label} arrow>
          <motion.div
            initial={{ opacity: 0, y: 10 }}
            animate={{ opacity: 1, y: 0 }}
            transition={{ delay: index * 0.1 }}
            whileHover={{ scale: 1.15, y: -3 }}
            whileTap={{ scale: 0.95 }}
          >
            <IconButton 
              sx={{ 
                bgcolor: `${format.color}15`, 
                color: format.color,
                '&:hover': { bgcolor: `${format.color}25` }
              }}
            >
              {format.icon}
            </IconButton>
          </motion.div>
        </Tooltip>
      ))}
    </Box>
  );
};

const ReportCard = ({ report }) => {
  const hasMultipleRuns = report.runs.length > 1;
  const [selectedRun, setSelectedRun] = useState(report.runs[report.runs.length - 1]);

  return (
    <motion.div layout initial={{ opacity: 0, scale: 0.9 }} animate={{ opacity: 1, scale: 1 }} transition={{ duration: 0.4, type: "spring" }}>
      <Paper elevation={0} sx={{ p: 3, borderRadius: 3, border: '1px solid', borderColor: 'divider', background: 'linear-gradient(145deg, #ffffff 0%, #f8f9fa 100%)', overflow: 'hidden' }}>
        <Box sx={{ display: 'flex', justifyContent: 'space-between', alignItems: 'flex-start', mb: 2 }}>
          <Box>
            <Typography variant="h6" fontWeight="700" color="primary.main">{report.title}</Typography>
            <Typography variant="body2" color="text.secondary">{report.description}</Typography>
          </Box>
          <Chip size="small" icon={selectedRun.status === 'SUCCESS' ? <CheckCircleIcon /> : <WarningIcon />} label={selectedRun.status} color={selectedRun.status === 'SUCCESS' ? 'success' : 'error'} variant="outlined" />
        </Box>
        <Divider sx={{ my: 2 }} />

        <AnimatePresence mode="wait">
          {hasMultipleRuns && (
            <motion.div initial={{ height: 0, opacity: 0 }} animate={{ height: 'auto', opacity: 1 }} exit={{ height: 0, opacity: 0 }} style={{ overflow: 'hidden' }}>
              <Typography variant="caption" fontWeight="bold" color="text.secondary" sx={{ mb: 1, display: 'block' }}>SELECT RUN</Typography>
              <Box sx={{ display: 'flex', gap: 1, mb: 3, flexWrap: 'wrap' }}>
                {report.runs.map((run) => {
                  const isSelected = selectedRun.runId === run.runId;
                  return (
                    <motion.div key={run.runId} whileHover={{ scale: 1.05 }} whileTap={{ scale: 0.95 }} onClick={() => setSelectedRun(run)} style={{ cursor: 'pointer' }}>
                      <Box sx={{ px: 2, py: 0.5, borderRadius: '20px', border: '1px solid', borderColor: isSelected ? 'primary.main' : 'divider', bgcolor: isSelected ? 'primary.main' : 'transparent', color: isSelected ? 'white' : 'text.primary', transition: 'background-color 0.2s', display: 'flex', alignItems: 'center', gap: 1 }}>
                        <Typography variant="body2" fontWeight={isSelected ? 600 : 400}>Run {run.runId}</Typography>
                        <Typography variant="caption" sx={{ opacity: 0.8 }}>({run.timestamp})</Typography>
                      </Box>
                    </motion.div>
                  );
                })}
              </Box>
            </motion.div>
          )}
        </AnimatePresence>

        <Box sx={{ textAlign: 'center', mt: hasMultipleRuns ? 0 : 2 }}>
          <Typography variant="subtitle2" color="text.secondary">Available Downloads</Typography>
          <DownloadActions />
        </Box>
      </Paper>
    </motion.div>
  );
};

// --- Main Screen ---

export default function AdhocReportsScreen() {
  const [etlDate, setEtlDate] = useState(null);
  const [reports, setReports] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    Promise.all([fetchEtlData(), fetchReports()]).then(([date, rep]) => {
      setEtlDate(date);
      setReports(rep);
      setLoading(false);
    });
  }, []);

  if (loading) {
    return (
      <Box sx={{ display: 'flex', height: '100vh', alignItems: 'center', justifyContent: 'center' }}>
        <CircularProgress />
      </Box>
    );
  }

  // Intercept if ETL date is NOT the last day of the month
  if (!isLastDayOfMonth(etlDate)) {
    return (
      <Container maxWidth="sm" sx={{ py: 10 }}>
        <motion.div initial={{ opacity: 0, y: 20 }} animate={{ opacity: 1, y: 0 }} transition={{ duration: 0.5 }}>
          <Paper elevation={0} sx={{ p: 5, borderRadius: 4, textAlign: 'center', border: '1px solid', borderColor: 'divider', bgcolor: '#fafafa' }}>
            <motion.div initial={{ scale: 0 }} animate={{ scale: 1 }} transition={{ delay: 0.2, type: "spring", stiffness: 150 }}>
              <EventBusyIcon sx={{ fontSize: 80, color: 'text.secondary', mb: 2, opacity: 0.5 }} />
            </motion.div>
            <Typography variant="h5" fontWeight="bold" gutterBottom color="text.primary">
              Not a Month-End Cycle
            </Typography>
            <Typography variant="body1" color="text.secondary" sx={{ mb: 4 }}>
              Adhoc Reports (YSA and PNL) are strictly generated as part of the month-end activity. 
              The current ETL Date is <strong>{etlDate}</strong>.
            </Typography>
            <Button 
              variant="contained" 
              color="primary" 
              startIcon={<ArrowBackIcon />}
              disableElevation
              sx={{ borderRadius: 2, px: 3, py: 1 }}
              onClick={() => { /* Navigation logic to dashboard here */ console.log("Navigate back"); }}
            >
              Return to Dashboard
            </Button>
          </Paper>
        </motion.div>
      </Container>
    );
  }

  return (
    <Container maxWidth="lg" sx={{ py: 6 }}>
      {/* ETL Date Banner */}
      <motion.div initial={{ y: -20, opacity: 0 }} animate={{ y: 0, opacity: 1 }} transition={{ duration: 0.5 }}>
        <Paper elevation={0} sx={{ p: 3, mb: 4, borderRadius: 3, bgcolor: 'primary.dark', color: 'white', display: 'flex', alignItems: 'center', gap: 2, boxShadow: '0 8px 32px rgba(0, 0, 0, 0.1)' }}>
          <Box sx={{ p: 1.5, bgcolor: 'rgba(255,255,255,0.1)', borderRadius: 2, display: 'flex' }}>
            <CalendarMonthIcon fontSize="large" />
          </Box>
          <Box>
            <Typography variant="h5" fontWeight="bold">Month-End Adhoc Reports</Typography>
            <Typography variant="subtitle1" sx={{ opacity: 0.8 }}>Data loaded as of ETL Date: <strong>{etlDate}</strong></Typography>
          </Box>
        </Paper>
      </motion.div>

      {/* Reports Grid */}
      <Grid container spacing={4}>
        {reports.map((report) => (
          <Grid item xs={12} md={6} key={report.id}>
            <ReportCard report={report} />
          </Grid>
        ))}
      </Grid>
    </Container>
  );
}
