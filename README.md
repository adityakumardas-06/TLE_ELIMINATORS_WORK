# TLE_ELIMINATORS_WORK
// We'll build this using React with Tailwind CSS for styling
// and ShadCN/UI components. This is a simplified version with mock data.

import { useEffect, useState } from 'react';
import { Card, CardContent } from '@/components/ui/card';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Table, TableHeader, TableRow, TableHead, TableBody, TableCell } from '@/components/ui/table';
import { Switch } from '@/components/ui/switch';
import { LineChart, Line, XAxis, YAxis, Tooltip, ResponsiveContainer, BarChart, Bar } from 'recharts';

const mockStudents = [
  {
    id: 1,
    name: 'Alice Verma',
    email: 'alice@example.com',
    phone: '9876543210',
    handle: 'alicev',
    currentRating: 1450,
    maxRating: 1600,
    lastUpdated: '2025-06-13',
    contests: [
      { date: '2025-06-01', ratingChange: +50, rank: 1200, unsolved: 1 },
      { date: '2025-05-01', ratingChange: -20, rank: 1900, unsolved: 3 },
    ],
    problems: [
      { rating: 1600 },
      { rating: 1400 },
      { rating: 1800 },
      { rating: 1500 },
    ],
    heatmap: {
      '2025-06-01': 1,
      '2025-06-02': 0,
      '2025-06-03': 2,
      '2025-06-04': 1,
    },
    inactiveDays: 0,
    reminderCount: 2,
    disableReminder: false,
  },
];

export default function StudentTable() {
  const [students, setStudents] = useState(mockStudents);
  const [selected, setSelected] = useState(null);
  const [dark, setDark] = useState(false);

  const getRatingBuckets = (problems) => {
    const buckets = {};
    problems.forEach(p => {
      const bucket = Math.floor(p.rating / 200) * 200;
      buckets[bucket] = (buckets[bucket] || 0) + 1;
    });
    return Object.entries(buckets).map(([rating, count]) => ({ rating, count }));
  };

  return (
    <div className={dark ? 'dark' : ''}>
      <div className="flex justify-between items-center p-4">
        <h1 className="text-xl font-bold">Student Progress Management</h1>
        <Switch checked={dark} onCheckedChange={() => setDark(!dark)} />
      </div>
      <Table>
        <TableHeader>
          <TableRow>
            <TableHead>Name</TableHead>
            <TableHead>Email</TableHead>
            <TableHead>Phone</TableHead>
            <TableHead>CF Handle</TableHead>
            <TableHead>Current Rating</TableHead>
            <TableHead>Max Rating</TableHead>
            <TableHead>Last Updated</TableHead>
            <TableHead>Actions</TableHead>
          </TableRow>
        </TableHeader>
        <TableBody>
          {students.map((s) => (
            <TableRow key={s.id}>
              <TableCell>{s.name}</TableCell>
              <TableCell>{s.email}</TableCell>
              <TableCell>{s.phone}</TableCell>
              <TableCell>{s.handle}</TableCell>
              <TableCell>{s.currentRating}</TableCell>
              <TableCell>{s.maxRating}</TableCell>
              <TableCell>{s.lastUpdated}</TableCell>
              <TableCell>
                <Button onClick={() => setSelected(s)}>Details</Button>
              </TableCell>
            </TableRow>
          ))}
        </TableBody>
      </Table>

      {selected && (
        <Card className="m-4">
          <CardContent>
            <h2 className="text-lg font-semibold">{selected.name}'s Profile</h2>
            <h3 className="mt-4 font-bold">Contest History</h3>
            <ResponsiveContainer width="100%" height={200}>
              <LineChart data={selected.contests.map(c => ({ date: c.date, ratingChange: c.ratingChange }))}>
                <XAxis dataKey="date" />
                <YAxis />
                <Tooltip />
                <Line type="monotone" dataKey="ratingChange" stroke="#8884d8" />
              </LineChart>
            </ResponsiveContainer>

            <ul className="mt-2">
              {selected.contests.map((c, i) => (
                <li key={i}>Date: {c.date}, Rank: {c.rank}, Rating Change: {c.ratingChange}, Unsolved: {c.unsolved}</li>
              ))}
            </ul>

            <h3 className="mt-4 font-bold">Problem Solving Data</h3>
            <p>Total Problems Solved: {selected.problems.length}</p>
            <p>Average Rating: {Math.round(selected.problems.reduce((a, b) => a + b.rating, 0) / selected.problems.length)}</p>
            <p>Most Difficult Solved: {Math.max(...selected.problems.map(p => p.rating))}</p>

            <ResponsiveContainer width="100%" height={200}>
              <BarChart data={getRatingBuckets(selected.problems)}>
                <XAxis dataKey="rating" />
                <YAxis />
                <Tooltip />
                <Bar dataKey="count" fill="#82ca9d" />
              </BarChart>
            </ResponsiveContainer>

            <h3 className="mt-4 font-bold">Submission Heatmap</h3>
            <div className="grid grid-cols-7 gap-2">
              {Object.entries(selected.heatmap).map(([date, count]) => (
                <div key={date} className={`w-6 h-6 rounded ${count > 0 ? 'bg-green-500' : 'bg-gray-300'}`} title={`${date}: ${count} submissions`} />
              ))}
            </div>

            <h3 className="mt-4 font-bold">Reminder & Inactivity</h3>
            <p>Inactivity: {selected.inactiveDays} days</p>
            <p>Reminders Sent: {selected.reminderCount}</p>
            <p>Auto Email: {selected.disableReminder ? 'Disabled' : 'Enabled'}</p>
          </CardContent>
        </Card>
      )}
    </div>
  );
}
