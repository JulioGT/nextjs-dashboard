## Next.js Dashboard App

A modern dashboard application built with Next.js 16, TypeScript, Tailwind CSS, and PostgreSQL. This app features user authentication, invoice management, customer tracking, and revenue analytics.

### Features

- **User Authentication**: Secure login system using NextAuth.js
- **Dashboard Overview**: Revenue charts, recent invoices, and key metrics
- **Invoice Management**: Create, edit, and view invoices with pagination
- **Customer Management**: View and manage customer data
- **Responsive Design**: Mobile-friendly interface with Tailwind CSS
- **Type Safety**: Full TypeScript support
- **Database Integration**: PostgreSQL with optimized queries

### Tech Stack

- **Framework**: Next.js 16 with App Router
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **Database**: Supabase (PostgreSQL)
- **Authentication**: NextAuth.js
- **Forms**: React Hook Form with Zod validation
- **Icons**: Heroicons
- **Package Manager**: pnpm

## Prerequisites

Before running this application, make sure you have the following installed:

- **Node.js**: Version 18 or higher
- **pnpm**: Package manager
- **PostgreSQL**: Database server (local or cloud)

### Installing Prerequisites

#### macOS (using Homebrew)

```bash
# Install Node.js
brew install node

# Install pnpm
npm install -g pnpm

# Install PostgreSQL
brew install postgresql
brew services start postgresql
```

#### Other Platforms

- **Node.js**: Download from [nodejs.org](https://nodejs.org/)
- **pnpm**: `npm install -g pnpm`
- **PostgreSQL**: See [postgresql.org](https://www.postgresql.org/download/)

## Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/your-username/nextjs-dashboard.git
   cd nextjs-dashboard
   ```

2. **Install dependencies**

   ```bash
   pnpm install
   ```

3. **Set up environment variables**

   ```bash
   cp .env.example .env.local
   ```

   Edit `.env.local` and fill in your Supabase values:

   - Get these from your Supabase project dashboard (Settings > API)
   - `NEXT_PUBLIC_SUPABASE_URL`: Your Supabase project URL
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY`: Your Supabase anon/public key
   - `POSTGRES_URL`: Your Supabase database connection string (Settings > Database)
   - `NEXTAUTH_SECRET`: A random secret key for NextAuth (generate with `openssl rand -base64 32`)
   - `NEXTAUTH_URL`: Your app's URL (http://localhost:3000 for development)

4. **Set up Supabase Database**

   **Create a Supabase Project:**

   - Go to [supabase.com](https://supabase.com) and create an account
   - Create a new project
   - Wait for the database to be set up (usually takes a few minutes)

   **Get your environment variables:**

   - Go to Settings > API in your Supabase dashboard
   - Copy the Project URL and anon/public key
   - Go to Settings > Database
   - Copy the connection string (use the "Connection pooling" version for `POSTGRES_URL`)

   **Seed the database with sample data:**

   ```bash
   # Start the dev server first
   pnpm dev

   # Then seed the database by visiting:
   # http://localhost:3000/seed
   ```

## Running the Application

### Development

Start the development server with hot reload:

```bash
pnpm dev
```

The app will be available at [http://localhost:3000](http://localhost:3000).

### Production Build

1. **Build the application**

   ```bash
   pnpm build
   ```

2. **Start the production server**
   ```bash
   pnpm start
   ```

The production app will run on [http://localhost:3000](http://localhost:3000).

### Linting

Run ESLint to check code quality:

```bash
pnpm lint
```

## Project Structure

```
├── app/                    # Next.js app directory
│   ├── dashboard/         # Dashboard pages
│   ├── login/            # Authentication page
│   ├── lib/              # Utility functions and data
│   └── ui/               # Reusable UI components
├── auth.config.ts        # NextAuth configuration
├── auth.ts              # NextAuth setup
├── next.config.ts       # Next.js configuration
├── tailwind.config.ts   # Tailwind CSS configuration
└── tsconfig.json        # TypeScript configuration
```

## Security Best Practices

### Environment Variables

- **Never commit** `.env` or `.env.local` files to version control
- Use `.env.example` as a template for required environment variables
- Generate strong secrets for `NEXTAUTH_SECRET` (32+ characters)
- Use different secrets for development and production

### Database Security

- Use SSL connections in production (`ssl: 'require'`)
- Never hardcode database credentials in source code
- Use environment variables for all sensitive configuration
- Regularly update database passwords and rotate secrets

### Authentication

- Passwords are hashed using bcrypt before storage
- Session management handled securely by NextAuth.js
- Protected routes require authentication

### Deployment Security

When deploying to production:

1. Set `NODE_ENV=production`
2. Use HTTPS in production
3. Configure proper CORS settings
4. Enable security headers
5. Use a reverse proxy (nginx) for additional security
6. Regularly update dependencies

## Deployment

### Vercel (Recommended)

1. Connect your GitHub repository to Vercel
2. Add environment variables in Vercel dashboard:
   - `POSTGRES_URL`
   - `NEXTAUTH_SECRET`
   - `NEXTAUTH_URL`
3. Deploy automatically on push

### Other Platforms

For deployment to other platforms, ensure:

- Environment variables are set
- PostgreSQL database is accessible
- Build command: `pnpm build`
- Start command: `pnpm start`

## Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Make your changes and test thoroughly
4. Run linting: `pnpm lint`
5. Commit your changes: `git commit -am 'Add your feature'`
6. Push to the branch: `git push origin feature/your-feature`
7. Submit a pull request

## Troubleshooting

### Common Issues

1. **Database connection errors**

   - Verify `POSTGRES_URL` is correct
   - Ensure PostgreSQL is running
   - Check firewall settings

2. **Authentication issues**

   - Verify `NEXTAUTH_SECRET` is set
   - Check `NEXTAUTH_URL` matches your domain

3. **Build errors**
   - Clear `.next` folder: `rm -rf .next`
   - Reinstall dependencies: `pnpm install`
   - Check Node.js version compatibility

### Getting Help

- Check the [Next.js documentation](https://nextjs.org/docs)
- Review [NextAuth.js docs](https://next-auth.js.org/)
- Open an issue on GitHub for bugs or feature requests

## License

This project is licensed under the MIT License - see the LICENSE file for details.

---

Built with ❤️ using Next.js
